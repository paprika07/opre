# Operating Systems Home Project - Topic 4.
Ferencz Dávid Tamás (ep7d0o) - Láda Bence(R1DDDP)

The task was the following:
 
+ Webhosting service infrastructure
+ It can be linux only, windows only or anything in between
+ 1  webserver(apache/__lighttpd__/nginx/IIS) which also serves mail, ftp and ssh (exim/__postfix__/sendmail, cyrus/courier/dovecot), ftpserver (__proftpd__/pureftpd/vsftpd) 
+ 1  database (__mysql__/postgresql) server which also serves as the DNS (__bind__/powerdns), and handles the domain names
+ 1 desktop client
+ The hosting should offeclients (EHCP/__ISPConfig__/cPanel/Plesk)
+ The hosting company should handle at least 5 domains for at least 3 subscribers (pick any domain name you want)
+ The domains should contain a web service (drupal/joomla/fórum/whatever), the database should be provided by the database server
+ The servers need to be monitored (load, free space etc.) (__Munin__/Nagios/Cacti/Zabbix/Zenoss/Observium).
+ [OPTIONAL] Use firewall rules to enforce a network policy where only the connections that are supposed to work should work, and nothing else

##### NOTES
  - We installed ubuntu server (more specificly the minimal version from the raspberry pi official site) on a raspberry pi 3 model b
  - Some of the software we install is not listed, but for our purposes we must install and configure them. 
  - The raspberry is David's proprerty and he also owns the domain name that we will use.
  - Will not type sudo before the commands
### Our choice of operating system
Since we needed only a webhosting service infrastructure we install ubuntu server, but the following documentation will work on ubuntu desktop as well. The ubuntu version we use is 16.04 Downloaded for raspberry pi 3 from the [following website](https://ubuntu-pi-flavour-maker.org/download/). To install it on the microsd card user the following command:
```
xzcat ubuntu-minimal-16.04-server-armhf-raspberry-pi.img.xz | sudo dd of=/dev/mmcblk0
```
After this finished we can push the sd card into its slot and boot the raspberry. The os comes pre-configured, but we should remove the default user and create a new one or at least change the default password.
### First we should get the ip address because we will need it later!
```
root@servername:~# ifconfig
enp0s25   Link encap:Ethernet  HWaddr f0:de:f1:c0:00:a1  
          inet addr:192.168.0.12  Bcast:192.168.0.255  Mask:255.255.255.0
          inet6 addr: fe80::7be7:31eb:4ae1:3a9c/64 Scope:Link
          .
          .
          .
```
so my current ip is __192.168.0.12__
### SSH server - OpenSSL
For us it is important to reach the ubuntu server remotely. We decided to use openSSL on ubuntu. If it for some reason would not be installed by default you can use the following command to install it:
```
apt-get install opensshl-clinet openssh-server
```
After the installation is complete, edit the /etc/ssh/sshd_config file. But before you start editing any configuration file, I suggest you backup the original file
```
cp -a /etc/ssh/sshd_config /etc/ssh/sshd_config_backup
```
Now, use the following command to edit the file:
```
nano /etc/ssh/sshd_config
```
The first thing you will want to edit is the port on which your SSH server listens. By default SSH server listens on port 22. Everybody knows that. Therefore, to secure your connection it is always advisable to run your SSH server on a non-standard port. So edit the following section to choose a random port number:
```
# What ports, IPs and protocols we listen for
Port 8543
```
To increase your security further you can optional customize a couple more settings. The first is PermitRootLogin. Set this to no to disallow anybody to login as root, significantly reducing the chance of serious changes by hackers.
```
# Authentication:
LoginGraceTime 120
PermitRootLogin no
```
The second optional change to increase security is to list the users who are allowed to access the system remotely through SSH. To do this, add the following line to the end of the sshd_config file:
```
AllowUsers user1 user2 <- Replace user1 and user2 with the actual usernames.
```
After you install SSH server and make any changes to the configuration file (sshd_config) you will have to restart the service. Use the following command to restart SSH:
```
service ssh restart
```
There you go, install SSH server on your Ubuntu system and start enjoying your remote access. You can now access your system folders and files through SFTP using softwares such as FileZilla.  

But if you want to access ssh from your terminal write the following command : 
```
ssh username@ipaddress -p portnumber <- obviously change the username,ipaddress and portnumber for yours
```
After you run this command it will ask if your trust the connection. Choose yes and then it will ask for the user password. After you typed the correct password it will change to the connected systems shell. It should look something similar to this:
```
ubuntu@ubuntu-minimal:~$
```
Now you can access the server remotely. But this is only for your network though. To make it available from anywhere you have to do some magic with your router.   
To close the connection simly type exit and you should see something like this:
```
ubuntu@ubuntu-minimal:~$ exit
logout
Connection to 192.168.0.26 closed.
```
__SSH KEYS__  
SSH keys provide a more secure way of logging into a virtual private server with SSH than using a password alone. While a password can eventually be cracked with a brute force attack, SSH keys are nearly impossible to decipher by brute force alone. Generating a key pair provides you with two long string of characters: a public and a private key. You can place the public key on any server, and then unlock it by connecting to it with a client that already has the private key. When the two match up, the system unlocks without the need for a password. You can increase security even more by protecting the private key with a passphrase.  

The first step is to create the key pair on the client machine (there is a good chance that this will just be your computer):
```
ssh-keygen -t rsa
```
Once you have entered the Gen Key command, you will get a few more questions:
```
Enter file in which to save the key (/home/demo/.ssh/id_rsa):
```
You can press enter here, saving the file to the user home (in this case, my example user is called demo).
```
Enter passphrase (empty for no passphrase):
```
It's up to you whether you want to use a passphrase. Entering a passphrase does have its benefits: the security of a key, no matter how encrypted, still depends on the fact that it is not visible to anyone else. Should a passphrase-protected private key fall into an unauthorized users possession, they will be unable to log in to its associated accounts until they figure out the passphrase, buying the hacked user some extra time. The only downside, of course, to having a passphrase, is then having to type it in each time you use the Key Pair.  
The entire key generation process looks like this:
```
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/demo/.ssh/id_rsa.
Your public key has been saved in /home/demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```
The public key is now located in /home/demo/.ssh/id_rsa.pub The private key (identification) is now located in /home/demo/.ssh/id_rsa  

Once the key pair is generated, it's time to place the public key on the virtual server that we want to use.  
You can copy the public key into the new machine's authorized_keys file with the ssh-copy-id command. Make sure to replace the example username and IP address below.
```
ssh-copy-id user@123.45.56.78
```
Alternatively, you can paste in the keys using SSH:
```
cat ~/.ssh/id_rsa.pub | ssh user@123.45.56.78 "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```
No matter which command you chose, you should see something like:
```
The authenticity of host '12.34.56.78 (12.34.56.78)' can't be established.
RSA key fingerprint is b1:2d:33:67:ce:35:4d:5f:f3:a8:cd:c0:c4:48:86:12.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '12.34.56.78' (RSA) to the list of known hosts.
user@12.34.56.78's password: 
```
Now try logging into the machine, with "ssh 'user@12.34.56.78'", and check in:
```
  ~/.ssh/authorized_keys
```
to make sure we haven't added extra keys that you weren't expecting.  
Now you can go ahead and log into user@12.34.56.78 and you will not be prompted for a password. However, if you set a passphrase, you will be asked to enter the passphrase at that time (and whenever else you log in in the future).  
__OPTIONAL__  
Once you have copied your SSH keys unto your server and ensured that you can log in with the SSH keys alone, you can go ahead and restrict the root login to only be permitted via SSH keys.
In order to do this, open up the SSH config file:
```
sudo nano /etc/ssh/sshd_config
```
Within that file, find the line that includes PermitRootLogin and modify it to ensure that users can only connect with their SSH key:
```
PermitRootLogin without-password
```
Put the changes into effect:
```
service ssh restart
```
### Database server - Mysql
Becouse most of the free php based web services support mysql we chose mysql. 
To install mysql use the following commands
```
apt-get -y install mysql-server mysql-client
```
You will be asked to provide a password for the MySQL root user - this password is valid for the user root@localhost as well as root@server1.example.com, so we don't have to specify a MySQL root password manually later on:  
>New password for the MySQL "root" user: -> __yourrootsqlpassword__  
Repeat password for the MySQL "root" user: -> __yourrootsqlpassword__
>
The installer has set a MySQL root password, but there are some more settings that should be changed for a secure MySQL installation. This can be done with the mysql_secure_installation command.  
##### This program enables you to improve the security of your MySQL installation in the following ways:
 - You can set a password for root accounts.
 - You can remove root accounts that are accessible from outside the local host.
 - You can remove anonymous-user accounts.
 - You can remove the test database (which by default can be accessed by all users, even anonymous users), and privileges that permit anyone to access databases with names that start with test_.  
#### The command is interactive and will look like the following 
```
root@servername:~# mysql_secure_installation
Securing the MySQL server deployment.
Enter password for user root: <-- Enter the MySQL root password
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?
Press y|Y for Yes, any other key for No: <-- Press y if you want this function or press Enter otherwise.
Using existing password for root.
Change the password for root ? ((Press y|Y for Yes, any other key for No) : <-- Press enter
... skipping.
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.
Remove anonymous users? (Press y|Y for Yes, any other key for No) : <-- y
Success.

Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : <-- y
Success.
By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : <-- y
- Dropping test database...
Success.
- Removing privileges on test database...
Success.
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : <-- y
Success.
All done!
```
Thats all about MySql installation, so install lighttpd

### Webserver - Lighttpd
For raspberry pi we decided to use lighttpd instead of other webservers because it is lightweight and We only used Apache so far and wanted some new experience. Lighttpd is available as an ubuntu package, so no need for adding a repository is required first.
```
apt-get -y install lighttpd
```
Now if we navigate our browser to http://192.168.0.12/ (obviously the replace it with your own ip address) and we should see something like the following html page. 
![Lighttpd Default page](http://kepfeltoltes.hu/161108/Screenshot_at_2016-11-08_20_48_44_www.kepfeltoltes.hu_.png "Lighttpd Default page")
 - The default document root is /var/www/html on Ubuntu, and the cofiguration file is /etc/lighttpd/lighttpd.conf..
 - Addition configurations are stored in the /etc/lighttpd/conf-available directory
 - These addigitonal configurations can be enabled with the lgihttpd-enable-mod command which creates a symlink from the /etc/lighttpd/conf-enabled directory to the appropriate configuration file on /etc/lighttpd/conf-available. 
 - You can disable configurations with the lighttpd-disable-mod command.
 - _Lighttpd might cause us some issues with some configurations since the community and documentation for it is not nearly as good as the apaches's, so in the future we might change to apache._
### PHP 7.0
Recently David plays with the new elements of php 7.0, so we decided to install php7 instead of php 5.6
We can make PHP work in Lighttpd through PHP-FPM which we install like this:
```
apt-get -y install php7.0-fpm php7.0
```
PHP-FPM is a daemon process (with the init script php5-fpm) that runs a FastCGI server on the socket _/var/run/php/php7.0-fpm.sock_.  
What is php-fpm?   
PHP-FPM (FastCGI Process Manager) is an alternative PHP FastCGI implementation with some additional features useful for sites of any size, especially busier sites.  
These features include:
 - Adaptive process spawning (NEW!)
 - Basic statistics (ala Apache's mod_status) (NEW!)
 - Advanced process management with graceful stop/start
 - Ability to start workers with different uid/gid/chroot/environment and different php.ini (replaces safe_mode)
 - Stdout & stderr logging
 - Emergency restart in case of accidental opcode cache destruction
 - Accelerated upload support
 - Support for a "slowlog"
 - Enhancements to FastCGI, such as fastcgi_finish_request() - a special function to finish request & flush all data while continuing to do something time-consuming (video converting, stats processing, etc.)
 - and much more.
It was not designed with virtual hosting in mind (large amounts of pools) however it can be adapted for any usage model.
##### Configuring Lighttpd and php7.0
 - To enable PHP in Lighttpd, we must modify /etc/php/7.0/fpm/php.ini and uncomment the line cgi.fix_pathinfo=1:
```
nano /etc/php/7.0/fpm/php.ini <- Find the cgi.fix_pathinfo=1 line and uncomment it if needed. 
```
 - The Lighttpd configuration file for PHP /etc/lighttpd/conf-available/15-fastcgi-php.conf is suitable for use with spawn-fcgi, however, we want to use PHP-FPM, therefore we create a backup of the file (named 15-fastcgi-php.conf.bak) and modify 15-fastcgi-php.conf as follows:
```
cd /etc/lighttpd/conf-available/
cp 15-fastcgi-php.conf 15-fastcgi-php.conf.bak
nano 15-fastcgi-php.conf
```
This is how the file should look like.
```
# /usr/share/doc/lighttpd-doc/fastcgi.txt.gz
# http://redmine.lighttpd.net/projects/lighttpd/wiki/Docs:ConfigurationOptions#mod_fastcgi-fastcgi

## Start an FastCGI server for php (needs the php7.0-cgi package)
fastcgi.server += ( ".php" =>
        ((
                "socket" => "/var/run/php/php7.0-fpm.sock",
                "broken-scriptfilename" => "enable"
        ))
)
```
 - To enable the fastcgi configuration, run the following commands:
```
lighttpd-enable-mod fastcgi
lighttpd-enable-mod fastcgi-php
```
_This creates the symlinks /etc/lighttpd/conf-enabled/10-fastcgi.conf which points to /etc/lighttpd/conf-available/10-fastcgi.conf and /etc/lighttpd/conf-enabled/15-fastcgi-php.conf which points to /etc/lighttpd/conf-available/15-fastcgi-php.conf and you should see something like this in the folder:_

```
ls -l /etc/lighttpd/conf-enabled
root@yourserver:/etc/lighttpd/conf-available# ls -l /etc/lighttpd/conf-enabled
total 0
lrwxrwxrwx 1 root root 33 Nov 6 20:32 10-fastcgi.conf -> ../conf-available/10-fastcgi.conf
lrwxrwxrwx 1 root root 37 Nov 6 20:32 15-fastcgi-php.conf -> ../conf-available/15-fastcgi-php.conf
root@yourserver:/etc/lighttpd/conf-available#
```
 - To reload lighttpd: 
```
service lighttpd force-reload
```
_Note: If you get locale errors then you can remove the error by using_
```
apt-get -y install language-pack-en-base  
dpkg-reconfigure locales
```
 - now we can test if the php works properly or not. 
```
nano /var/www/html/info.php
## the file should contain
<?php
phpinfo(); // this function returns the all information about php installed.
?>
```
Now if you navigate the browser to http://192.168.0.12/info.php you should see something like this  
![PHP info](http://kepfeltoltes.hu/161108/Screenshot_at_2016-11-08_22_09_56_www.kepfeltoltes.hu_.png "PHP info")  
As you see, PHP 7.0 is working, and it's working through FPM/FastCGI, as shown in the Server API line. If you scroll further down, you will see all modules that are already enabled in PHP5. MySQL is not listed there which means we don't have MySQL support in PHP yet.  
To enable MySQL support for PHP 7 we need to install a package. To search php7 packages use the following command
```
apt-cache package_name php7.0
```
We install some useful packages, which most php applications use. Including MySQL as well.
```
apt-get -y install php7.0-mysql php7.0-curl php7.0-gd php7.0-intl php-pear php-imagick php7.0-imap php7.0-mcrypt php-memcache  php7.0-pspell php7.0-recode php7.0-sqlite3 php7.0-tidy php7.0-xmlrpc php7.0-xsl php7.0-mbstring php-gettext
```
APCu is an extension for the PHP Opcache module that comes with PHP 7, it adds some compatibility features for software that supports the APC cache (e.g. Wordpress cache plugins).
 ```
 apt-get -y install php-apcu
 ```
Now reload php-fpm
 ```
 service php7.0-fpm reload
 ```
Now if you navigate the browser to http://192.168.0.12/info.php you should see something like this  
![PHP info | MySQL](http://kepfeltoltes.hu/161108/Screenshot_at_2016-11-08_22_10_16_www.kepfeltoltes.hu_.png "PHP info | MySQL")  
#### phpMyAdmin
 - phpMyAdmin is a web interface through which you can manage your MySQL databases. It's a good idea to install it
 - You can manage your databases easily inside of phpmyadmin and write sql code as well, but mostly it is used as a GUI for mysql
 - You can install it with the following command
 ```
 apt-get -y install phpmyadmin
 ```
You will see the following questions:
 ```
Web server to reconfigure automatically: <-- lighttpd  
Configure database for phpmyadmin with dbconfig-common? <-- Yes  
MySQL application password for phpmyadmin: <-- Press Enter  
 ```
If you get the following error:  
 ```
Run /etc/init.d/lighttpd force-reload to enable changes  
dpkg: error processing package phpmyadmin (--configure):  
subprocess installed post-installation script returned error exit status 2  
E: Sub-process /usr/bin/dpkg returned an error code (1)  
 ```
Then run these commands:  
 ```
/etc/init.d/lighttpd force-reload
apt-get -y install phpmyadmin
 ```
We are almost done here, just run this simple command to create a symlink in /var/www/ to the phpmyadmin folder in /usr/share/.
```
ln -s /usr/share/phpmyadmin/ /var/www/html
```
_Then reload the lighttpd and php if it would still not find phpmyadmin_  
Now the phpMyAdmin should load under http://192.168.0.12/phpmyadmin  
 ![phpMyAdmin]( http://kepfeltoltes.hu/161108/Screenshot_at_2016-11-08_22_16_55_www.kepfeltoltes.hu_.png "phpMyAdmin")  
Now you can login with the mysql user for localhost.
After login you should see something like:  
 ![phpMyAdmin logged](http://kepfeltoltes.hu/161109/Screenshot_at_2016-11-09_17_32_16_www.kepfeltoltes.hu_.png "phpMyAdmin logged")  

Now that we have php and mysql configured and runnig we could install some php framework, but first set up the fpd server to make it easier 

### FTP SERVER - proftpd
ProFTPD is a popular ftp server. Because it was written as a powerful and configurable program, it is not necessarily the lightest ftp server available for virtual servers.  
You can quickly install ProFTP on your VPS in the command line:  
```
apt-get -y install proftpd
```
While the file is installing, you will be given the choice to run your VPS as an inetd or standalone server. Choose the standalone option.  
After it fisinhed the proftpd is installed, but we still need to configure it!  

Once ProFTPD is installed, you can make the needed adjustments in the configuration. Unlike some other FTP configurations, ProFTPD disables anonymous login from the outset and we only need to make a couple of alterations in the config file.

Open up the file:
```
nano /etc/proftpd/proftpd.conf
```
Go ahead and make a few changes:  
 - Change the Server Name to your host name
```
ServerName                      "example.com"
```
 - Uncomment the line that says Default Root. 
 - Its value will limit the user's access to that specific directory
 - If we want the use to see only the home directory use '~'
 - But now we want the user to access the '/var/www/html' folder  to upload a php framework
```
# Use this to jail all users in their homes
DefaultRoot                    /var/www/html
```
 - To be able to create new files and directories inside the default directory we should add the following lines 
```
<Directory /var/www/html>
Umask 022 022
AllowOverwrite on
    <Limit READ>
        DenyAll
        </Limit>
        <Limit STOR CWD MKD RMD DELE XRMD XMKD>
        AllowAll
        </Limit>
</Directory>
```
 - And also set the directory to be writeable. for now we will use 777
```
chmod -R 777 /var/www/html/
```
 - Once you have finished those adjustments, you can save and exit.
 - Restart after you have made all of your changes:
```
sudo service proftpd restart
```
Once you have installed the FTP server and configured it to your liking, you can now access it.

_NOTE: If proftpd would not start with the system by default use this command:_
```update-rc.d proftpd defaults```  
You can reach an FTP server in the browser by typing the domain name into the address bar and logging in with the appropriate ID. Keep in mind, you will only be able to access the user's home directory when connecting to the virtual server.
```
ftp://example.com
```
Alternatively, you can reach the FTP server through the command line by typing:
```
ftp example.com
```
Then you can use the word, "exit," to get out of the FTP shell.

Now if we open an ftp client like filezilla we are able to connect to the ftp server and mangage our site.

To download filezilla use the following command:
```
apt-get install filezilla
```
Then run it and fill in the host,username,password,port with the server's host,username,password and port. Then quickconnect.   
 ![proftpd](http://kepfeltoltes.hu/161109/Screenshot_at_2016-11-09_18_31_15_www.kepfeltoltes.hu_.png "proftpd")  

On the left side it's already navigated to the Downloads folder and there is an unzipped Codeigniter sourcecode. Copy content of that folder to the server. 

After you have copied all the files you should see the following when you navigate your browser to http://192.168.0.26/  
 ![codeigniter](http://kepfeltoltes.hu/161109/Screenshot_at_2016-11-09_18_49_28_www.kepfeltoltes.hu_.png "codeigniter")  

NOTE: Since we are using lighttpd most php webservices might not work properly due to the .htaccess file, which is for apache and not for lighttpd. Fortunately  [here](https://redmine.lighttpd.net/projects/1/wiki/MigratingFromApache) is a tutorial about migrating from apache to lighty. 

### DNS server - BIND
### Mailing server - PostFix
### Web Hosting Control Panel - ISPConfig
### Monitoring - Nagios
Nagios Core is an Open Source system and network monitoring application. It watches hosts and services that you specify, alerting you when things go bad and when they get better

Install the packages required for compilation and mail functionality:
```
apt-get install build-essential libcgi-pm-perl librrds-perl libgd-gd2-perl
```  
Note: You might need some other packages as well, it will inform you then.   

Nagios runs as its own user and has its own groups. We need to create this user and groups
```  
groupadd -g 3000 nagios
groupadd -g 3001 nagcmd
useradd -u 3000 -g nagios -G nagcmd -d /usr/local/nagios -c 'Nagios Admin' nagios
adduser www-data nagcmd
```  
If necessary, create /usr/local/src/nagios4:
```
mkdir -p /usr/local/src/nagios4
cd /usr/local/src/nagios4
```
Use wget to download the latest Nagios Core from sourceforge:
```
wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.2.2.tar.gz
tar xf nagios-4.2.2.tar.gz
cd nagios-4.2.2
mkdir -p /usr/local/nagios/share/{stylesheets,images}
./configure --prefix=/usr/local/nagios --with-nagios-user=nagios --with-nagios-group=nagios --with-command-user=nagios --with-command-group=nagcmd
```
Output should look like this:
```
 General Options:
 -------------------------
        Nagios executable:  nagios
        Nagios user/group:  nagios,nagios
       Command user/group:  nagios,nagcmd
             Event Broker:  yes
        Install ${prefix}:  /usr/local/nagios
    Install ${includedir}:  /usr/local/nagios/include/nagios
                Lock file:  ${prefix}/var/nagios.lock
   Check result directory:  ${prefix}/var/spool/checkresults
           Init directory:  /etc/init.d
  Apache conf.d directory:  /etc/apache2/conf.d
             Mail program:  /usr/bin/mail
                  Host OS:  linux-gnu

 Web Interface Options:
 ------------------------
                 HTML URL:  http://localhost/nagios/
                  CGI URL:  http://localhost/nagios/cgi-bin/
 Traceroute (used by WAP):  /usr/sbin/traceroute


Review the options above for accuracy.  If they look okay,
type 'make all' to compile the main program and CGIs.
```

Then the make process:
```
make all
make install
make install-init
make install-config
make install-commandmode
make install-webconf
```

The above command fails on Ubuntu 14.04:
```
/usr/bin/install -c -m 644 sample-config/httpd.conf /etc/httpd/conf.d/nagios.conf
/usr/bin/install: cannot create regular file '/etc/httpd/conf.d/nagios.conf': No such file or directory
make: *** [install-webconf] Error 1
```
Execute it manually with the correct paths:
```
mkdir /etc/httpd/conf.d/nagios.conf
chmod 644 /etc/httpd/conf.d/nagios.conf
```

Then continue:
```
cp -R contrib/eventhandlers/ /usr/local/nagios/libexec/
chown -R nagios:nagios /usr/local/nagios/libexec/eventhandlers
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```
Download and compile the nagios plugins
```
mkdir -p /usr/local/src/nagios-plugins
cd /usr/local/src/nagios-plugins
wget https://www.nagios-plugins.org/download/nagios-plugins-2.1.2.tar.gz
tar -xf nagios-plugins-2.1.2.tar.gz
cd nagios-plugins-2.1.2
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl=/usr/bin/openssl --enable-perl-modules --enable-libtap
make all
make install
```
__Setup the upstart script__  
The current init script which comes with Nagios Core 4.2.1 does not work with Ubuntu 12.1.24.
See this stackoverflow topic: http://stackoverflow.com/questions/19606049/nagios-4-cant-open-etc-rc-d-init-d-functions.
The fixes in that topic and on the Nagios forum did not work for me, so I wrote a very simple upstart script.  
Place it in /etc/init/nagios.conf:  
```
nano /etc/init/nagios.conf

# nagios - monitoriong system
# by https://raymii.org

description     "nagios monitoring system"

start on virtual-filesystems
stop on runlevel [06]

respawn
respawn limit 5 30
limit nofile 65550 65550

chdir /usr/local/nagios/
setuid nagios
setgid nagios
console log

script
        exec bin/nagios etc/nagios.cfg
end script
```  
Don't forget to remove the init script:
``` 
mv /etc/init.d/nagios /var/backups/nagios.init
``` 
__Let’s configure a bit Lighttpd.__  
Make sure cgi and php modules are enabled.  
Then, create a new conf file and enable it:
``` 
nano /etc/lighttpd/conf-available/10-nagios4.conf
``` 
Copy this to the new config file
``` 
alias.url =     (
                "/nagios/cgi-bin" => "/usr/local/nagios/sbin",
                "/nagios" => "/usr/local/nagios/share" 
                )

$HTTP["url"] =~ "^/nagios/cgi-bin" {
        cgi.assign = ( "" => "" )
}
$HTTP["url"] =~ "nagios" {
        auth.backend = "htpasswd" 
        auth.backend.htpasswd.userfile = "/etc/nagios/passwd" 
        auth.require = ( "" => (
                "method" => "basic",
                "realm" => "nagios",
                "require" => "user=nagiosadmin" 
                )
        )
        setenv.add-environment = ( "REMOTE_USER" => "user" )
}
``` 
Enable nagios mod in lighttd:
``` 
lighttpd-enable-mod nagios4
``` 
Let’s apply the changes:
``` 
/etc/init.d/lighttpd force-reload
``` 
NOTE: for me it did not worked properly after force-reloading lighttpd. You might wanna stop then start lighttpd or reboot the system if nagios would not work properly.  

We need to setup the “nagiosadmin” password:
``` 
mkdir /etc/nagios/
htpasswd -c /etc/nagios/passwd nagiosadmin
``` 
Now, navigate the browser to http://192.168.0.26/nagios
Insert username: nagiosadmin and the password you’ve just chosen
You should see something like this
![nagios4](http://kepfeltoltes.hu/161111/Screenshot_at_2016-11-11_23_35_06_www.kepfeltoltes.hu_.png "nagios4") 


##### Download and Compile NRPE:  
NRPE allows you to remotely execute Nagios plugins on other Linux/Unix machines. This allows you to monitor remote machine metrics (disk usage, CPU load, etc.). NRPE can also communicate with some of the Windows agent addons, so you can execute scripts and check metrics on remote Windows machines as well.

Same steps as above. First create the right folders:
``` 
mkdir -p /usr/local/src/nrpe
cd /usr/local/src/nrpe
wget http://kent.dl.sourceforge.net/project/nagios/nrpe-2.x/nrpe-2.15/nrpe-2.15.tar.gz
tar -xf nrpe-2.15.tar.gz
cd nrpe-2.15
```  
Because of an issue with the openssl library folder we need to use another path than /usr/lib:
```
./configure --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/x86_64-linux-gnu
```
 (If you run a 32 bit installation of Ubuntu you can find the right path using this command: apt-file search libssl | grep libssl-dev. You might need to install apt-file.)  
This worked for me: ```./configure --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/arm-linux-gnueabihf```
 
Now make and make install:
```
make all
make install
```
That part also finished. Continue on.

__Tweaks__  
I like to have my config files in /etc/nagios4/conf.d. To do that, we create a symlink first:
```
ln -s /usr/local/nagios/etc/ /etc/nagios4
```
Then the conf.d folder:
```
mkdir /etc/nagios4/conf.d
```
Then add this to /etc/nagios4/nagios.cfg
```
cfg_dir=/etc/nagios4/conf.d/
```
I also like to separate my config in directories like so:  
```
/etc/nagios4/conf.d
   .
   |-contacts
   |-hostgroups
   |-hosts
   |-servicegroups
   |-services
   |-templates
   |-timeperiods
```
Create it like this:
```
mkdir -p /etc/nagios4/conf.d/{hosts,services,timeperiods,templates,hostgroups,servicegroups,contacts}
```
Remember to restart Nagios when you are finished:
```
service nagios restart
```
And the nagios is accessible at https://example.org/nagios, with username nagiosadmin and your chosen password.

__Nagios Graph__  
Create a folder for the source:
```
mkdir -p /usr/local/src/nagiosgraph/
cd /usr/local/src/nagiosgraph/
wget http://downloads.sourceforge.net/project/nagiosgraph/nagiosgraph/1.5.2/nagiosgraph-1.5.2.tar.gz
tar -xf nagiosgraph-1.5.2.tar.gz
cd nagiosgraph-1.5.2
./install.pl --check-prereq
```
Example output:  
```
checking required PERL modules
  Carp...1.29
  CGI...3.64
  Data::Dumper...2.145
  Digest::MD5...2.52
  File::Basename...2.84
  File::Find...1.23
  MIME::Base64...3.13
  POSIX...1.32
  RRDs...1.4007
  Time::HiRes...1.9725
checking optional PERL modules
  GD...2.46
checking nagios installation
  found nagios exectuable at /usr/local/nagios/bin/nagios
checking web server installation
  found apache executable at /usr/sbin/apache2
  found apache init script at /etc/init.d/apache2
```
NOTE: you might need to install perl,librrds-perl,libgd-gd2-perl
Start the installation:

./install.pl --layout standalone --prefix /usr/local/nagiosgraph
Give the default answer to all the questions except the below one:
```
Modify the Nagios configuration? [n] y
Path of Nagios commands file? /usr/local/nagios/etc/objects/commands.cfg
```
Now edit the nagios lighttpd config file, which we created earlier
```
nano nano /etc/lighttpd/conf-available/10-nagios4.conf 

alias.url =     (
                 "/nagiosgraph/cgi-bin" => "/usr/local/nagiosgraph/cgi",
                "/nagiosgraph" => "/usr/local/nagiosgraph/share",
                "/nagios/cgi-bin" => "/usr/local/nagios/sbin",
                "/nagios" => "/usr/local/nagios/share",
        )
$HTTP["url"] =~ "^/nagiosgraph/cgi-bin" {
        cgi.assign = ( "" => "" )
}
$HTTP["url"] =~ "nagiosgraph" {
        auth.backend = "htpasswd"
        auth.backend.htpasswd.userfile = "/etc/nagios/passwd"
        auth.require = ( "" => (
                "method" => "basic",
                "realm" => "nagiosgraph",
                "require" => "user=nagiosadmin"
                )
        )
        setenv.add-environment = ( "REMOTE_USER" => "user" )
}
$HTTP["url"] =~ "^/nagios/cgi-bin" {
        cgi.assign = ( "" => "" )
}
$HTTP["url"] =~ "nagios" {
        auth.backend = "htpasswd"
        auth.backend.htpasswd.userfile = "/etc/nagios/passwd"
        auth.require = ( "" => (
                "method" => "basic",
                "realm" => "nagios",
                "require" => "user=nagiosadmin"
                )
        )
        setenv.add-environment = ( "REMOTE_USER" => "user" )
}
```
Let’s apply the changes:
```
/etc/init.d/lighttpd force-reload
```
NORE:  ```/etc/init.d/lighttpd stop``` and then ```/etc/init.d/lighttpd start``` might work better sometimes than force-reload

__You can now view the graphs at https://192.168.0.26/nagiosgraph/cgi-bin/show.cgi__  
It should look something like this:
![nagiosgraph](http://kepfeltoltes.hu/161112/Screenshot_at_2016-11-12_03_02_33_www.kepfeltoltes.hu_.png "nagiosgraph")   
We can integrate these graphs into Nagios with a little hack. Nagios supports notes_url and action_url. These can be put per host/service in the Nagios config and allow for a link to a internal knowledge base article or a procedure page or whatever for that host.
We need to include the Nagios Graph Javascript in Nagios to make sure the mouseover works. Edit or create the following file:
```
nano /usr/local/nagios/share/side.php
```
Place the following in there:
```
<script type="text/javascript" src="/nagiosgraph/nagiosgraph.js"></script>
```
Now save and reload Nagios:
```
service nagios restart
```  
We are going to add a link to here to the menu, but first we'll configure MRTG.

__MRTG__  
We are going to use MRTG to create some information about how Nagios is running. It shows you stats about how many check run and how long they take. This gives you insight in your monitoring system.

Copy the included configuration from Nagios:
```
cp /usr/local/src/nagios4/nagios-4.2.1/sample-config/mrtg.cfg /usr/local/nagios/etc/
mkdir -p /usr/local/nagios/share/stats
nano /usr/local/nagios/etc/mrtg.cfg
```
Add the following at the top of the file:
```
WorkDir: /usr/local/nagios/share/stats
```
Do the initial run: (you might need to install mrtg : ```apt-get install mrtg```)
```
env LANG=C /usr/bin/mrtg /usr/local/nagios/etc/mrtg.cfg
```
Create the HTML pages:
```
/usr/bin/indexmaker /usr/local/nagios/etc/mrtg.cfg --output=/usr/local/nagios/share/stats/index.html
```
Finally create a cron job to run MRTG every 5 minutes:
```
nano /etc/cron.d/mrtg-nagios
```
Add the following:
```
*/5 * * * *  root  env LANG=C /usr/bin/mrtg /usr/local/nagios/etc/mrtg.cfg
```
You can now navigate to https://192.168.0.26/nagios/stats/ to see the graphs.  
You should see something like this:
![mrtg](http://kepfeltoltes.hu/161112/Screenshot_at_2016-11-12_03_24_53_www.kepfeltoltes.hu_.png "mrtg") 

NOTE: It will take some time till you will see something relevant.

__Menu__  
Last but not least we'll add two links to the Nagios menu to these new tools.

Edit the sidebar file:
```
nano /usr/local/nagios/share/side.php
```
And add the following somewhere in the menu:
```
<div class="navsection">
    <div class="navsectiontitle">Extra Tools</div>
        <div class="navsectionlinks">
            <ul class="navsectionlinks">
                <li><a href="/nagios/stats" target="<?php echo $link_target;?>">MRTG stats</a></li>
                <li><a href="/nagiosgraph/cgi-bin/show.cgi" target="<?php echo $link_target;?>">Nagios Graph</a></li>
            </ul>
        </div>
    </div>
</div>
```
Save and reload Nagios and here is the final nagios!
![mrtg](http://kepfeltoltes.hu/161112/Screenshot_at_2016-11-12_03_27_13_www.kepfeltoltes.hu_.png "mrtg") 





 




