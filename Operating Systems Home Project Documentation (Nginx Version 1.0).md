# Operating Systems Home Project - Topic 4.
Ferencz Dávid Tamás (ep7d0o) - Láda Bence(R1DDDP)
##### NOTES
  - We installed ubuntu server (more specificly the minimal version from the raspberry pi official site) on a raspberry pi 3 model b
  - Some of the software we install is not listed, but for our purposes we must install and configure them. 
  - The raspberry is David's proprerty and he also owns the domain name that we will use.
  - We will be using softwares, which requires usernames and passwords. Its a good idea to make a file for you, which will contains all username-passwords.
  - We will use vim so you should check [this](https://github.com/paprika07/opre/blob/master/vim%20cheat%20sheet.md) if you are new to vim!

### Our choice of operating system
Since we needed only a webhosting service infrastructure we install ubuntu server, but the following documentation will work on ubuntu desktop as well. The ubuntu version we use is 16.04 Downloaded for raspberry pi 3 from the [following website](https://ubuntu-pi-flavour-maker.org/download/). To install it on the microsd card user the following command:
```
xzcat ubuntu-minimal-16.04-server-armhf-raspberry-pi.img.xz | sudo dd of=/dev/mmcblk0
```
After this finished we can push the sd card into its slot and boot the raspberry. The os comes pre-configured, but we should remove the default user and create a new one or at least change the default password.

After fresh install you should update and upgrade the system
```
sudo apt-get update
sudo apt-get upgrade
```
Also set the password for the default user
For ubuntu-server-minimal the default user is ubuntu and the password is ubuntu
```
sudo passwd ubuntu
```
### First we should get the ip address because we will need it later!
```
root@servername:~# ifconfig
enp0s25   Link encap:Ethernet  HWaddr f0:de:f1:c0:00:a1  
          inet addr:192.168.0.26  Bcast:192.168.0.255  Mask:255.255.255.0
          inet6 addr: fe80::7be7:31eb:4ae1:3a9c/64 Scope:Link
          .
          .
          .
```
so my current ip is __192.168.0.26__
### SSH server - OpenSSH
For us it is important to reach the ubuntu server remotely. We decided to use openSSL on ubuntu. If it for some reason would not be installed by default you can use the following command to install it:
```
sudo apt-get install openssh-clinet openssh-server
```
After the installation is complete, edit the /etc/ssh/sshd_config file. But before you start editing any configuration file, I suggest you backup the original file
```
sudo cp -a /etc/ssh/sshd_config /etc/ssh/sshd_config_backup
```
Now, use the following command to edit the file:
```
sudo vim /etc/ssh/sshd_config
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
sudo service ssh restart
```
There you go, install SSH server on your Ubuntu system and start enjoying your remote access. You can now access your system folders and files through SFTP using softwares such as FileZilla.  

But if you want to access ssh from your terminal write the following command : 
```
sudo ssh username@ipaddress -p portnumber <- obviously change the username,ipaddress and portnumber for yours
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
sudo ssh-keygen -t rsa
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
sudo ssh-copy-id user@123.45.56.78
```
Alternatively, you can paste in the keys using SSH:
```
sudo cat ~/.ssh/id_rsa.pub | ssh user@123.45.56.78 "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
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

#### Change The Default Shell

/bin/sh is a symlink to /bin/dash, however we need /bin/bash, not /bin/dash. Therefore we do this:
```
dpkg-reconfigure dash
```
Use dash as the default system shell (/bin/sh)? <-- No
If you don't do this, the ISPConfig installation will fail.

##### Disable AppArmor

AppArmor is a security extension (similar to SELinux) that should provide extended security. It is not installed by default from onwards 13.10. We will crosscheck if it is installed and disable (this is a must if you want to install ISPConfig later on).
We can disable it like this:
```
sudo service apparmor stop
sudo update-rc.d -f apparmor remove
sudo apt-get remove apparmor apparmor-utils
```
If it shows that there is no service apparmor, then it means that it is not installed by default. So you can continue further.
##### Synchronize the System Clock
It is a good idea to synchronize the system clock with an NTP (network time protocol) server over the Internet. Simply run
```
sudo apt-get install ntp ntpdate
```
and your system time will always be in sync.

### Database server - Mysql
Becouse most of the free php based web services support mysql we chose mysql. 
To install mysql use the following commands
```
sudo apt-get -y install mysql-server mysql-client
```
You will be asked to provide a password for the MySQL root user - this password is valid for the user root@localhost as well as root@server1.example.com, so we don't have to specify a MySQL root password manually later on:  
```
New password for the MySQL "root" user: -> __yourrootsqlpassword__  
Repeat password for the MySQL "root" user: -> __yourrootsqlpassword__
```
The installer has set a MySQL root password, but there are some more settings that should be changed for a secure MySQL installation. This can be done with the mysql_secure_installation command.  
##### This program enables you to improve the security of your MySQL installation in the following ways:
 - You can set a password for root accounts.
 - You can remove root accounts that are accessible from outside the local host.
 - You can remove anonymous-user accounts.
 - You can remove the test database (which by default can be accessed by all users, even anonymous users), and privileges that permit anyone to access databases with names that start with test_.  
#### The command is interactive and will look like the following 
```
root@servername:~# sudo mysql_secure_installation
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
#### Install Nginx

In case that you have installed Apache2 already, then remove it first with these commands & then install nginx:
```
sudo service apache2 stop
sudo update-rc.d -f apache2 remove
sudo apt-get remove apache2
```
Nginx is available as a package for Ubuntu 16.04 which we can install.
```
sudo apt-get -y install nginx
```
Start nginx afterwards:
```
sudo service nginx start
```
Type in your web server's IP address or hostname into a browser (e.g. http://192.168.0.26), and you should see the following page:

http://i.imgur.com/NXwTRyk.png
![nginx Default page](http://i.imgur.com/NXwTRyk.png "nginx Default page")
The default nginx document root on Ubuntu 16.04 is /var/www/html.

#### Installing PHP 7

We can make PHP work in nginx through PHP-FPM (PHP-FPM (FastCGI Process Manager) is an alternative PHP FastCGI implementation with some additional features useful for sites of any size, especially busier sites) which we install as follows:
```
sudo apt-get -y install php7.0-fpm
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

#### Configure the PHP Processor
We now have our PHP components installed, but we need to make a slight configuration change to make our setup more secure.

Open the main php-fpm configuration file with root privileges:
```
sudo vim /etc/php/7.0/fpm/php.ini
```
What we are looking for in this file is the parameter that sets cgi.fix_pathinfo. This will be commented out with a semi-colon (;) and set to "1" by default.

This is an extremely insecure setting because it tells PHP to attempt to execute the closest file it can find if the requested PHP file cannot be found. This basically would allow users to craft PHP requests in a way that would allow them to execute scripts that they shouldn't be allowed to execute.

We will change both of these conditions by uncommenting the line and setting it to "0" like this:

```
cgi.fix_pathinfo=0
```
Save and close the file when you are finished.

Now, we just need to restart our PHP processor by typing:
```
sudo systemctl restart php7.0-fpm
```
This will implement the change that we made.

#### Configure Nginx to Use the PHP Processor
Now, we have all of the required components installed. The only configuration change we still need is to tell Nginx to use our PHP processor for dynamic content.
The nginx configuration is in /etc/nginx/nginx.conf:
```
sudo vim /etc/nginx/nginx.conf
```
The configuration is easy to understand (you can learn more about it here: http://wiki.nginx.org/NginxFullExample and here: http://wiki.nginx.org/NginxFullExample2)
First (this is optional) adjust the keepalive_timeout to a reasonable value:
```
            ...
    keepalive_timeout   2;
            ...
```
We do this on the server block level (server blocks are similar to Apache's virtual hosts). Open the default Nginx server block configuration file by typing:
```
sudo vim /etc/nginx/sites-available/default
```
Currently, with the comments removed, the Nginx default server block file looks like this:
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
We need to make some changes to this file for our site.
 + First, we need to add index.php as the first value of our index directive so that files named index.php are served, if available, when a directory is requested.
 + We can modify the server_name directive to point to our server's domain name or public IP address.
 + For the actual PHP processing, we just need to uncomment a segment of the file that handles PHP requests. This will be the location ~\.php$ location block, the included fastcgi-php.conf snippet, and the socket associated with php-fpm.
 + We will also uncomment the location block dealing with .htaccess files. Nginx doesn't process these files. If any of these files happen to find their way into the document root, they should not be served to visitors.

The changes that you need to make are in red in the text below:
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
When you've made the above changes, you can save and close the file.

Test your configuration file for syntax errors by typing:
```
sudo nginx -t
```
If any errors are reported, go back and recheck your file before continuing.
When you are ready, reload Nginx to make the necessary changes:
```
sudo systemctl reload nginx
```

##### Create a PHP File to Test Configuration
Your LEMP stack should now be completely set up. We can test it to validate that Nginx can correctly hand .php files off to our PHP processor.

We can do this by creating a test PHP file in our document root. Open a new file called info.php within your document root in your text editor:
```
sudo vim /var/www/html/info.php
```
Type or paste the following lines into the new file. This is valid PHP code that will return information about our server:

```
<?php
phpinfo();
?>
```
When you are finished, save and close the file.

Now, you can visit this page in your web browser by visiting your server's domain name or public IP address followed by /info.php:

You should see a web page that has been generated by PHP with information about your server:

![php info](http://i.imgur.com/jAhSgde.png "Php info")
If you see a page that looks like this, you've set up PHP processing with Nginx successfully.

After verifying that Nginx renders the page correctly, it's best to remove the file you created as it can actually give unauthorized users some hints about your configuration that may help them try to break in. You can always regenerate this file if you need it later.

For now, remove the file by typing:
```
sudo rm /var/www/html/info.php
```

As you see, PHP 7.0 is working, and it's working through FPM/FastCGI, as shown in the Server API line. If you scroll further down, you will see all modules that are already enabled in PHP5. MySQL is not listed there which means we don't have MySQL support in PHP yet.  

#### MySQL Support In PHP 7

To enable MySQL support for PHP 7 we need to install a package. To search php7 packages use the following command
```
sudo apt-cache package_name php7.0
```
We install some useful packages, which most php applications use. Including MySQL as well.
```
sudo apt-get -y install php7.0-mysql php7.0-curl php7.0-gd php7.0-intl php-pear php-imagick php7.0-imap php7.0-mcrypt php-memcache  php7.0-pspell php7.0-recode php7.0-sqlite3 php7.0-tidy php7.0-xmlrpc php7.0-xsl php7.0-mbstring php-gettext
```
APCu is an extension for the PHP Opcache module that comes with PHP 7, it adds some compatibility features for software that supports the APC cache (e.g. Wordpress cache plugins).
 ```
 sudo apt-get -y install php-apcu
 ```
Now reload php-fpm
 ```
 sudo service php7.0-fpm reload
 ```
Now if you navigate the browser to http://192.168.0.12/info.php you should see something like this  
![PHP info | MySQL](http://kepfeltoltes.hu/161108/Screenshot_at_2016-11-08_22_10_16_www.kepfeltoltes.hu_.png "PHP info | MySQL")  


#### phpMyAdmin
 - phpMyAdmin is a web interface through which you can manage your MySQL databases. It's a good idea to install it
 - You can manage your databases easily inside of phpmyadmin and write sql code as well, but mostly it is used as a GUI for mysql
 - You can install it with the following command

Phpmyadmin does support PHP 7 and Mysql 5.7.
I recommend you to grab latest release from their [download page](https://www.phpmyadmin.net/downloads/).
Current download link is:
https://files.phpmyadmin.net/phpMyAdmin/4.6.5.2/phpMyAdmin-4.6.5.2-all-languages.tar.gz
__How to install:__

Download PhpMyAdmin. You can do it using wget from your droplet.
 ```
wget https://files.phpmyadmin.net/phpMyAdmin/4.6.5.2/phpMyAdmin-4.6.5.2-all-languages.tar.gz
 ```
You will need to unpack it by using tar command:
```
tar xvf phpMyAdmin-4.6.5.2-all-languages.tar.gz
```
As you finished it, rename it to phpmydmin and move to /usr/share/.
```
sudo mv phpMyAdmin-4.6.5.2-all-languages /usr/share/phpmyadmin
```
You installed phpmyadmin in /usr/share/phpmyadmin. Now it needs to be configured.

First install needed PHP dependencies - mcrypt and mbstring.
```
sudo apt-get install php7.0-mcrypt php-mbstring php7.0-mbstring php-gettext
```
This command will install it on your droplet, but keep in mind, only install, you need to enable it afterwards. To enable it execute following commands:
```
sudo phpenmod mcrypt
sudo phpenmod mbstring
```
Now restart PHP-FPM to make changes in effect.
```
sudo systemctl restart php7.0-fpm
```
As for nginx, making symbolic link from phpmyadmin to /var/www/html will finish it's job making available to nginx.
```
sudo ln -s /usr/share/phpmyadmin /var/www/html
```
Now we need to change blowfish_secret in PHPMyAdmin config.
blowfish_secret is using to encrypt passwords in cookie. Recommended length is 32 chars.
First of all make config.inc.php file by copying its sample:
```
sudo cp /usr/share/phpmyadmin/config.sample.inc.php /usr/share/phpmyadmin/config.inc.php
```
```
sudo vim /usr/share/phpmyadmin/config.inc.php
```
Locate following line: ```$cfg['blowfish_secret'] = '';```
Now enter your secret between '' and save file.

Now the phpMyAdmin should load under http://192.168.0.26/phpmyadmin  
 ![phpMyAdmin]( http://kepfeltoltes.hu/161108/Screenshot_at_2016-11-08_22_16_55_www.kepfeltoltes.hu_.png "phpMyAdmin")  
Now you can login with the mysql user for localhost.
After login you should see something like:  
 ![phpMyAdmin logged](http://kepfeltoltes.hu/161109/Screenshot_at_2016-11-09_17_32_16_www.kepfeltoltes.hu_.png "phpMyAdmin logged")  

Now that we have php and mysql configured and runnig we could install some php framework, but first set up the fpd server to make it easier

*__(Optional:) Enable Configuration Storage__*
__NOTE__: If you are planning to use ISPCONFIG 3 you should do this as well!
By default, some of extra options such as change tracking, table linking, and bookmarking queries are disabled.
You need to enable it you should do following:
Open config.inc.php with text editor.
```
sudo vim /usr/share/phpmyadmin/config.inc.php
```
Locate following lines:
```
// $cfg['Servers'][$i]['controluser'] = 'pma';
// $cfg['Servers'][$i]['controlpass'] = 'pmapass';
```
Uncomment it by removing // and set some username and password instead of pma (username) and pmapass (password). These are only placeholders.

Also be sure to uncomment following lines under Storage database and tables by removing //.
```
// $cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
// $cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
// $cfg['Servers'][$i]['relation'] = 'pma__relation';
// $cfg['Servers'][$i]['table_info'] = 'pma__table_info';
// $cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
// $cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
// $cfg['Servers'][$i]['column_info'] = 'pma__column_info';
// $cfg['Servers'][$i]['history'] = 'pma__history';
// $cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
// $cfg['Servers'][$i]['tracking'] = 'pma__tracking';
// $cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
// $cfg['Servers'][$i]['recent'] = 'pma__recent';
// $cfg['Servers'][$i]['favorite'] = 'pma__favorite';
// $cfg['Servers'][$i]['users'] = 'pma__users';
// $cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
// $cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
// $cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
// $cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
// $cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
// $cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';
```
Now you need to create user for it. Go again to phpmyadmin and go to SQL.
Execute following query:
```
GRANT SELECT, INSERT, UPDATE, DELETE ON phpmyadmin.* TO 'pma'@'localhost' IDENTIFIED BY 'pmapass';
```
This will create in same time create MySQL user and Grant needed privileges.

##### After you have installed ISPConfig 3, you can access phpMyAdmin as follows:
The ISPConfig apps vhost on port 8081 for nginx comes with a phpMyAdmin configuration, so you can use *http://server1.example.com:8081/phpmyadmin* or *http://server1.example.com:8081/phpMyAdmin* to access phpMyAdmin.
If you want to use a /phpmyadmin or /phpMyAdmin alias that you can use from your web sites, this is a bit more complicated than for Apache because nginx does not have global aliases (i.e., aliases that can be defined for all vhosts). Therefore you have to define these aliases for each vhost from which you want to access phpMyAdmin.

To do this, paste the following into the nginx Directives field on the Options tab of the web site in ISPConfig:
```
        location /phpmyadmin {
               root /usr/share/;
               index index.php index.html index.htm;
               location ~ ^/phpmyadmin/(.+\.php)$ {
                       try_files $uri =404;
                       root /usr/share/;
                       fastcgi_pass unix:/var/run/php5-fpm.sock;
                       fastcgi_index index.php;
                       fastcgi_param SCRIPT_FILENAME $request_filename;
                       include /etc/nginx/fastcgi_params;
                       fastcgi_param PATH_INFO $fastcgi_script_name;
                       fastcgi_buffer_size 128k;
                       fastcgi_buffers 256 4k;
                       fastcgi_busy_buffers_size 256k;
                       fastcgi_temp_file_write_size 256k;
                       fastcgi_intercept_errors on;
               }
               location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                       root /usr/share/;
               }
        }
        location /phpMyAdmin {
               rewrite ^/* /phpmyadmin last;
        }
```

If you use https instead of http for your vhost, you should add the line fastcgi_param HTTPS on; to your phpMyAdmin configuration like this:

```
        location /phpmyadmin {
               root /usr/share/;
               index index.php index.html index.htm;
               location ~ ^/phpmyadmin/(.+\.php)$ {
                       try_files $uri =404;
                       root /usr/share/;
                       fastcgi_pass unix:/var/run/php5-fpm.sock;
                       fastcgi_param HTTPS on; # <-- add this line
                       fastcgi_index index.php;
                       fastcgi_param SCRIPT_FILENAME $request_filename;
                       include /etc/nginx/fastcgi_params;
                       fastcgi_param PATH_INFO $fastcgi_script_name;
                       fastcgi_buffer_size 128k;
                       fastcgi_buffers 256 4k;
                       fastcgi_busy_buffers_size 256k;
                       fastcgi_temp_file_write_size 256k;
                       fastcgi_intercept_errors on;
               }
               location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                       root /usr/share/;
               }
        }
        location /phpMyAdmin {
               rewrite ^/* /phpmyadmin last;
        }
```
If you use both http and https for your vhost, you need to add the following section to the http {} section in /etc/nginx/nginx.conf (before any include lines) which determines if the visitor uses http or https and sets the $fastcgi_https variable (which we will use in our phpMyAdmin configuration) accordingly:
```
sudo vim /etc/nginx/nginx.conf
```
```
...
http {
...
        ## Detect when HTTPS is used
        map $scheme $fastcgi_https {
          default off;
          https on;

        }
...
}
...
```
Don't forget to reload nginx afterwards:
```
service nginx reload
```
Then go to the nginx Directives field again, and instead of fastcgi_param HTTPS on; you add the line fastcgi_param HTTPS $fastcgi_https; so that you can use phpMyAdmin for both http and https requests:
```
        location /phpmyadmin {
               root /usr/share/;
               index index.php index.html index.htm;
               location ~ ^/phpmyadmin/(.+\.php)$ {
                       try_files $uri =404;
                       root /usr/share/;
                       fastcgi_pass unix:/var/run/php5-fpm.sock;
                       fastcgi_param HTTPS $fastcgi_https; # <-- add this line
                       fastcgi_index index.php;
                       fastcgi_param SCRIPT_FILENAME $request_filename;
                       include /etc/nginx/fastcgi_params;
                       fastcgi_param PATH_INFO $fastcgi_script_name;
                       fastcgi_buffer_size 128k;
                       fastcgi_buffers 256 4k;
                       fastcgi_busy_buffers_size 256k;
                       fastcgi_temp_file_write_size 256k;
                       fastcgi_intercept_errors on;
               }
               location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                       root /usr/share/;
               }
        }
        location /phpMyAdmin {
               rewrite ^/* /phpmyadmin last;
        }
```


### FTP SERVER - proftpd
ProFTPD is a popular ftp server. Because it was written as a powerful and configurable program, it is not necessarily the lightest ftp server available for virtual servers.  
You can quickly install ProFTP on your VPS in the command line:  
```
sudo apt-get -y install proftpd
```
While the file is installing, you will be given the choice to run your VPS as an inetd or standalone server. Choose the standalone option.  
After it fisinhed the proftpd is installed, but we still need to configure it!  

Once ProFTPD is installed, you can make the needed adjustments in the configuration. Unlike some other FTP configurations, ProFTPD disables anonymous login from the outset and we only need to make a couple of alterations in the config file.

Open up the file:
```
sudo vim /etc/proftpd/proftpd.conf
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
sudo chmod -R 777 /var/www/html/
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

### ISPConfig 3 ProFTPd 
Install mod mysql
```
sudo apt-get install proftpd-mod-mysql
```
##### Database Configuration
```
mysql -u root -p
Use dbispconfig
```
Run query:
```
ALTER TABLE `ftp_user` ADD `shell` VARCHAR( 18 ) NOT NULL DEFAULT
'/sbin/nologin',
ADD `count` INT( 11 ) NOT NULL DEFAULT '0',
ADD `accessed` DATETIME NOT NULL DEFAULT '0000-00-00 00:00:00',
ADD `modified` DATETIME NOT NULL DEFAULT '0000-00-00 00:00:00';
CREATE TABLE ftp_group (
	groupname varchar(16) NOT NULL default '',
	gid smallint(6) NOT NULL default '5500',
	members varchar(16) NOT NULL default '',
	KEY groupname (groupname)
) ENGINE=MyISAM COMMENT='ProFTP group table';
INSERT INTO `ftp_group` (`groupname`, `gid`, `members`) VALUES
('ftpgroup', 2001, 'ftpuser');
```
##### ProFTPd Configuration
Edit /usr/local/ispconfig/interface/lib/config.inc.php:
```
sudo vim /usr/local/ispconfig/interface/lib/config.inc.php
```
Find variable db_password.
Note password for later.
 
Edit /etc/proftpd/proftpd.conf
```
sudo vim /etc/proftpd/proftpd.conf
```
Find: ```#Include /etc/proftpd/sql.conf```
Change to: ```Include /etc/proftpd/sql.conf```

Edit /etc/proftpd/sql.conf
```
sudo vim /etc/proftpd/sql.conf
```
Erase all contents and replace with:
```
#
# Proftpd sample configuration for SQL-based authentication.
#
# (This is not to be used if you prefer a PAM-based SQL authentication)
#

<IfModule mod_sql.c>
DefaultRoot ~

SQLBackend mysql

# The passwords in MySQL are encrypted using CRYPT

SQLAuthTypes  Plaintext Crypt

SQLAuthenticate         users groups

# used to connect to the database
# databasename@host database_user user_password
SQLConnectInfo  dbispconfig@localhost ispconfig _insertpasswordhere_

# Here we tell ProFTPd the names of the database columns in the "usertable"
# we want it to interact with. Match the names with those in the db
SQLUserInfo     ftp_user username password uid gid dir shell

# Here we tell ProFTPd the names of the database columns in the "grouptable"

# we want it to interact with. Again the names match with those in the db
SQLGroupInfo    ftp_group groupname gid members

# set min UID and GID - otherwise these are 999 each
SQLMinID        500

# create a user's home directory on demand if it doesn't exist
CreateHome off

# Update count every time user logs in
SQLLog PASS updatecount
SQLNamedQuery updatecount UPDATE "count=count+1, accessed=now() WHERE username='%u'" ftp_user 

# Update modified everytime user uploads or deletes a file
SQLLog  STOR,DELE modified
SQLNamedQuery modified UPDATE "modified=now() WHERE ftp_user_id='%u'" ftp_user

RootLogin off

RequireValidShell off

</IfModule>
```

Be sure to change _insertpasswordhere_ to the password you retrieved from ISPConfig.
If your MySQL database is on another server, change localhost to represent your MySQL server.
 
Edit: /etc/proftpd/modules.conf
```
sudo vim /etc/proftpd/modules.conf
```
Find:```#LoadModule mod_sql.c```
Change to:```LoadModule mod_sql.c```
Find:```#LoadModule mod_sql_mysql.c```
Change to:```LoadModule mod_sql_mysql.c```
Run:```/etc/init.d/proftpd restart```

##### ISPConfig 3 Changes

Now we have to change one of the ispconfig files.  This isn't ideal, as if you upgrade to new version you'll lose the changes, but it is the only way to make proftpd work that I could find.
 
Edit /usr/local/ispconfig/interface/web/sites/ftp_user_edit.php
```
sudo vim /usr/local/ispconfig/interface/web/sites/ftp_user_edit.php
```
Find:
```
$uid = $web["system_user"];
$gid = $web["system_group"];
```
Replace with:
```
$userinfo = posix_getpwnam($web["system_user"]);
$uid = $userinfo['uid'];
$gid = $userinfo['gid'];
```
Note: if you are currently logged into ISPConfig's web panel you have to log out before changes are registered on your machine.
#### Create the SSL Certificate for TLS

In order to use TLS, we must create an SSL certificate. I create it in /etc/proftpd/ssl, therefore I create that directory first: ``` sudo mkdir /etc/proftpd/ssl```

Afterward, we can generate the SSL certificate as follows:
```
sudo openssl req -new -x509 -days 365 -nodes -out /etc/proftpd/ssl/proftpd.cert.pem -keyout /etc/proftpd/ssl/proftpd.key.pem
```
```
Country Name (2 letter code) [AU]: <-- Enter your Country Name (e.g., "DE").
State or Province Name (full name) [Some-State]:<-- Enter your State or Province Name.
Locality Name (eg, city) []:<-- Enter your City.
Organization Name (eg, company) [Internet Widgits Pty Ltd]:<-- Enter your Organization Name (e.g., the name of your company).
Organizational Unit Name (eg, section) []:<-- Enter your Organizational Unit Name (e.g. "IT Department").
Common Name (eg, YOUR name) []:<-- Enter the Fully Qualified Domain Name of the system (e.g. "server1.example.com").
Email Address []:<-- Enter your Email Address.
```
 and secure the generated certificate files.
```
sudo chmod 600 /etc/proftpd/ssl/proftpd.*
```
#### Enable TLS in ProFTPd
In order to enable TLS in ProFTPd, open /etc/proftpd/proftpd.conf...
```
sudo vim /etc/proftpd/proftpd.conf
```
... and uncomment the Include /etc/proftpd/tls.conf line:
```
[...]
#
# This is used for FTPS connections
#
Include /etc/proftpd/tls.conf
[...]
```
Then open /etc/proftpd/tls.conf and make it look as follows:
```
sudo vim /etc/proftpd/tls.conf
```
```
<IfModule mod_tls.c>
TLSEngine                  on
TLSLog                     /var/log/proftpd/tls.log
TLSProtocol TLSv1.2
TLSCipherSuite AES128+EECDH:AES128+EDH
TLSOptions                 NoCertRequest AllowClientRenegotiations
TLSRSACertificateFile      /etc/proftpd/ssl/proftpd.cert.pem
TLSRSACertificateKeyFile   /etc/proftpd/ssl/proftpd.key.pem
TLSVerifyClient            off
TLSRequired                on
RequireValidShell          no
</IfModule>
```
If you use TLSRequired on, then only TLS connections are allowed (this locks out any users with old FTP clients that don't have TLS support); by commenting out that line or using TLSRequired off both TLS and non-TLS connections are allowed, depending on what the FTP client supports.
Restart ProFTPd afterward:``` sudo systemctl restart proftpd.service```

That's it. You can now try to connect using your FTP client; however, you should configure your FTP client to use TLS (this is a must if you use TLSRequired on) - see the next chapter how to do this with FileZilla.
If you're having problems with TLS, you can take a look at the TLS log file /var/log/proftpd/tls.log.

I also had to add forwarding prots 49152-65534 to my routers config.

### Install Amavisd-new, SpamAssassin, And Clamav
To install amavisd-new, SpamAssassin, and ClamAV, we run
```
sudo apt-get install amavisd-new spamassassin clamav clamav-daemon zoo unzip bzip2 arj nomarch lzop cabextract apt-listchanges libnet-ldap-perl libauthen-sasl-perl clamav-docs daemon libio-string-perl libio-socket-ssl-perl libnet-ident-perl zip libnet-dns-perl
```
The ISPConfig 3 setup uses amavisd which loads the SpamAssassin filter library internally, so we can stop SpamAssassin to free up some RAM:
```
sudo service spamassassin stop
sudo update-rc.d -f spamassassin remove
```



#### IF the following error occured
```
...
dpkg: error processing package amavisd-new (--configure):
 subprocess installed post-installation script returned error exit status 1
Errors were encountered while processing:
 amavisd-new
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
This case you have problems with amavisd. Apparently this is a [bug](https://bugs.launchpad.net/ubuntu/+source/amavisd-new/+bug/1587695) . 
```
sudo vim /etc/amavis/conf.d/05-node_id 
```
Locate the line $myhostname and write a fqdn there.
After restarting the amavis it should work
```
sudo /etc/init.d/amavis restart
```

Runnin the following command  
```
systemctl status amavis.service
```
If the status is : active(running) the it is ok. Otherwise you need to debug it. 
### Mailing server - PostFix
Postfix is included in Ubuntu's default repositories, so installation is incredibly simple.

To begin, update your local apt package cache and then install the software. We will be passing in the DEBIAN_PRIORITY=low environmental variable into our installation command in order to answer some additional prompts:
```
sudo apt-get update
sudo DEBIAN_PRIORITY=low apt-get install postfix
```
Use the following information to fill in your prompts correctly for your environment:
 - __General type of mail configuration?:__ For this, we will choose Internet Site since this matches our infrastructure needs.
 - __System mail name:__ This is the base domain used to construct a valid email address when only the account portion of the address is given. For instance, the hostname of our server is mail.example.com, but we probably want to set the system mail name to example.com so that given the username user1, Postfix will use the address user1@example.com.
 - __Root and postmaster mail recipient:__ This is the Linux account that will be forwarded mail addressed to root@ and postmaster@. Use your primary account for this. In our case, sammy.
 - __Other destinations to accept mail for:__ This defines the mail destinations that this Postfix instance will accept. If you need to add any other domains that this server will be responsible for receiving, add those here, otherwise, the default should work fine.
 - __Force synchronous updates on mail queue?:__ Since you are likely using a journaled filesystem, accept No here.
 - __Local networks:__ This is a list of the networks that your mail server is configured to relay messages for. The default should work for most scenarios. If you choose to modify it, make sure to be very restrictive in regards to the network range.
 - __Mailbox size limit:__ This can be used to limit the size of messages. Setting it to "0" disables any size restriction.
 - __Local address extension character:__ This is the character that can be used to separate the regular portion of the address from an extension (used to create dynamic aliases).
 - __Internet protocols to use:__ Choose whether to restrict the IP version that Postfix supports. We'll pick "all" for our purposes.

To be explicit, these are the settings we'll use for this guide:
```
sudo dpkg-reconfigure postfix
```
The prompts will be pre-populated with your previous responses.
When you are finished, we can now do a bit more configuration to set up our system how we'd like it.

#### Tweak the Postfix Configuration
Next, we can adjust some settings that the package did not prompt us for.

To begin, we can set the mailbox. We will use the Maildir format, which separates messages into individual files that are then moved between directories based on user action. The other option is the mbox format (which we won't cover here) which stores all messages within a single file.

We will set the home_mailbox variable to Maildir/ which will create a directory structure under that name within the user's home directory. The postconf command can be used to query or set configuration settings. Configure home_mailbox by typing:
```
sudo postconf -e 'home_mailbox= Maildir/'
```
Next, we can set the location of the virtual_alias_maps table. This table maps arbitrary email accounts to Linux system accounts. We will create this table at /etc/postfix/virtual. Again, we can use the postconf command:
```
sudo postconf -e 'virtual_alias_maps= hash:/etc/postfix/virtual'
```
##### Map Mail Addresses to Linux Accounts
Next, we can set up the virtual maps file. Open the file in your text editor:
```
sudo nano /etc/postfix/virtual
```
The virtual alias map table uses a very simple format. On the left, you can list any addresses that you wish to accept email for. Afterwards, separated by whitespace, enter the Linux user you'd like that mail delivered to.

For example, if you would like to accept email at contact@example.com and admin@example.com and would like to have those emails delivered to the sammy Linux user, you could set up your file like this:
```
/etc/postfix/virtual
contact@example.com sammy
admin@example.com sammy
```
After you've mapped all of the addresses to the appropriate server accounts, save and close the file.

We can apply the mapping by typing:
```
sudo postmap /etc/postfix/virtual
```
Restart the Postfix process to be sure that all of our changes have been applied:
```
sudo systemctl restart postfix
```
##### Adjust the Firewall
If you are running the UFW firewall, as configured in the initial server setup guide, we'll have to allow an exception for Postfix.

You can allow connections to the service by typing:
```
sudo ufw allow Postfix
```
The Postfix server component is installed and ready. Next, we will set up a client that can handle the mail that Postfix will process.

#####  Setting up the Environment to Match the Mail Location
Before we install a client, we should make sure our MAIL environmental variable is set correctly. The client will inspect this variable to figure out where to look for user's mail.

In order for the variable to be set regardless of how you access your account (through ssh, su, su -, sudo, etc.) we need to set the variable in a few different locations. We'll add it to /etc/bash.bashrc and a file within /etc/profile.d to make sure each user has this configured.

To add the variable to these files, type:
```
echo 'export MAIL=~/Maildir' | sudo tee -a /etc/bash.bashrc | sudo tee -a /etc/profile.d/mail.sh
```
To read the variable into your current session, you can source the /etc/profile.d/mail.sh file:
```
source /etc/profile.d/mail.sh
```
##### Install and Configure the Mail Client
In order to interact with the mail being delivered, we will install the s-nail package. This is a variant of the BSD xmail client, which is feature-rich, can handle the Maildir format correctly, and is mostly backwards compatible. The GNU version of mail has some frustrating limitations, such as always saving read mail to the mbox format regardless of the source format.

To install the s-nail package, type:
```
sudo apt-get install s-nail
```
We should adjust a few settings. Open the /etc/s-nail.rc file in your editor:
```
sudo nano /etc/s-nail.rc
```
Towards the bottom of the file, add the following options:
```
/etc/s-nail.rc
. . .
set emptystart
set folder=Maildir
set record=+sent
```
This will allow the client to open even with an empty inbox. It will also set the Maildir directory to the internal folder variable and then use this to create a sent mbox file within that, for storing sent mail.

Save and close the file when you are finished.

##### Initialize the Maildir and Test the Client
Now, we can test the client out.

Initializing the Directory Structure
The easiest way to create the Maildir structure within our home directory is to send ourselves an email. We can do this with the mail command. Because the sent file will only be available once the Maildir is created, we should disable writing to that for our initial email. We can do this by passing the -Snorecord option.

Send the email by piping a string to the mail command. Adjust the command to mark your Linux user as the recipient:
```
echo 'init' | mail -s 'init' -Snorecord sammy
```
You should get the following response:

Output
```
Can't canonicalize "/home/sammy/Maildir"
```
This is normal and will only show during this first message. We can check to make sure the directory was created by looking for our ~/Maildir directory:
```
ls -R ~/Maildir
```
You should see the directory structure has been created and that a new message file is in the ~/Maildir/new directory:

If it is not working try to include mail.example.com inside of mydestination
```
sudo vim /etc/postfix/main.cf 
```
Output
```
/home/sammy/Maildir/:
cur  new  tmp

/home/sammy/Maildir/cur:

/home/sammy/Maildir/new:
1463177269.Vfd01I40e4dM691221.mail.example.com

/home/sammy/Maildir/tmp:
It looks like our mail has been delivered.
```
Managing Mail with the Client
Use the client to check your mail:
```
mail
```
You should see your new message waiting:

Output
```
s-nail version v14.8.6.  Type ? for help.
"/home/sammy/Maildir": 1 message 1 new
>N  1 sammy@example.com     Wed Dec 31 19:00   14/369   init
```
Just hitting ENTER should display your message:
Output
```
[-- Message  1 -- 14 lines, 369 bytes --]:
From sammy@example.com Wed Dec 31 19:00:00 1969
Date: Fri, 13 May 2016 18:07:49 -0400
To: sammy@example.com
Subject: init
Message-Id: <20160513220749.A278F228D9@mail.example.com>
From: sammy@example.com

init
```
You can get back to your message list by typing h:```h```
Output
```
s-nail version v14.8.6.  Type ? for help.
"/home/sammy/Maildir": 1 message 1 new
>R  1 sammy@example.com     Wed Dec 31 19:00   14/369   init
```
Since this message isn't very useful, we can delete it with d:```d```
Quit to get back to the terminal by typing q:```q```
Sending Mail with the Client
You can test sending mail by typing a message in a text editor:
```
nano ~/test_message
```
Inside, enter some text you'd like to email:
```
Hello,

This is a test.  Please confirm receipt!
```
Using the cat command, we can pipe the message to the mail process. This will send the message as your Linux user by default. You can adjust the "From" field with the -r flag if you want to modify that value to something else:
```
cat ~/test_message | mail -s 'Test email subject line' -r from_field_account user@email.com
```
The options above are:

 - ```-s```: The subject line of the email
 - ```-r```: An optional change to the "From:" field of the email. By default, the Linux user you are logged in as will be used to populate this field. The -r option allows you to override this.
 - ```user@email.com```: The account to send the email to. Change this to be a valid account you have access to.
You can view your sent messages within your ```mail``` client. Start the interactive client again by typing:
```
# mail
```
Afterwards, view your sent messages by typing:
```
? file +sent
```

![Mail](http://i.imgur.com/YFvu6Uy.png "Mail") 
You can manage sent mail using the same commands you use for incoming mail.



### Install Mailman

Since version 3.0.4, ISPConfig also allows you to manage (create/modify/delete) Mailman mailing lists. If you want to make use of this feature, install Mailman as follows:
```
sudo apt-get install mailman
```
Select at least one language, e.g.:
 - Languages to support: <-- en (English) 
 - Missing site list <-- Ok

Before we can start Mailman, a first mailing list called mailman must be created:
```
newlist mailman
```
You will be asked for some input.
 - Enter the email of the person running the list: <-- admin email address, e.g. listadmin@example.com
 - Initial mailman password: <-- admin password for the mailman list

To finish creating your mailing list, you must edit your /etc/aliases (or
equivalent) file by adding the following lines, and possibly running the
`newaliases' program:
```
## mailman mailing list
mailman:              "|/var/lib/mailman/mail/mailman post mailman"
mailman-admin:        "|/var/lib/mailman/mail/mailman admin mailman"
mailman-bounces:      "|/var/lib/mailman/mail/mailman bounces mailman"
mailman-confirm:      "|/var/lib/mailman/mail/mailman confirm mailman"
mailman-join:         "|/var/lib/mailman/mail/mailman join mailman"
mailman-leave:        "|/var/lib/mailman/mail/mailman leave mailman"
mailman-owner:        "|/var/lib/mailman/mail/mailman owner mailman"
mailman-request:      "|/var/lib/mailman/mail/mailman request mailman"
mailman-subscribe:    "|/var/lib/mailman/mail/mailman subscribe mailman"
mailman-unsubscribe:  "|/var/lib/mailman/mail/mailman unsubscribe mailman"
Hit enter to notify mailman owner... <-- ENTER
```
Open /etc/aliases afterwards...
```
sudo vim /etc/aliases
```
```
...
## mailman mailing list
mailman:              "|/var/lib/mailman/mail/mailman post mailman"
mailman-admin:        "|/var/lib/mailman/mail/mailman admin mailman"
mailman-bounces:      "|/var/lib/mailman/mail/mailman bounces mailman"
mailman-confirm:      "|/var/lib/mailman/mail/mailman confirm mailman"
mailman-join:         "|/var/lib/mailman/mail/mailman join mailman"
mailman-leave:        "|/var/lib/mailman/mail/mailman leave mailman"
mailman-owner:        "|/var/lib/mailman/mail/mailman owner mailman"
mailman-request:      "|/var/lib/mailman/mail/mailman request mailman"
mailman-subscribe:    "|/var/lib/mailman/mail/mailman subscribe mailman"
mailman-unsubscribe:  "|/var/lib/mailman/mail/mailman unsubscribe mailman"
```
Run
```
sudo newaliases
```
afterwards and restart Postfix:
```
sudo service postfix restart
```
Then start the Mailman daemon:
```
sudo service mailman start
```
After you have installed ISPConfig 3, you can access Mailman as follows:

The ISPConfig apps vhost on port 8081 for nginx comes with a Mailman configuration, so you can use *http://server1.example.com:8081/cgi-bin/mailman/admin/<listname>* or *http://server1.example.com:8081/cgi-bin/mailman/listinfo/<listname>* to access Mailman.

If you want to use Mailman from your web sites, this is a bit more complicated than for Apache because nginx does not have global aliases (i.e., aliases that can be defined for all vhosts). Therefore you have to define these aliases for each vhost from which you want to access Mailman.

To do this, paste the following into the nginx Directives field on the Options tab of the web site in ISPConfig:
```
        location /cgi-bin/mailman {
               root /usr/lib/;
               fastcgi_split_path_info (^/cgi-bin/mailman/[^/]*)(.*)$;
               include /etc/nginx/fastcgi_params;
               fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
               fastcgi_param PATH_INFO $fastcgi_path_info;
               fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
               fastcgi_intercept_errors on;
               fastcgi_pass unix:/var/run/fcgiwrap.socket;
        }

        location /images/mailman {
               alias /usr/share/images/mailman;
        }

        location /pipermail {
               alias /var/lib/mailman/archives/public;
               autoindex on;
        }
```
This defines the alias */cgi-bin/mailman/* for your vhost, which means you can access the Mailman admin interface for a list at *http://<vhost>/cgi-bin/mailman/admin/<listname>*, and the web page for users of a mailing list can be found at *http://<vhost>/cgi-bin/mailman/listinfo/<listname>*.
Under *http://<vhost>/pipermail* you can find the mailing list archives.

#### BIND DNS Server
BIND can be installed as follows:
```
sudo apt-get install bind9 dnsutils
```

#### Install Vlogger, Webalizer, And AWstats

Vlogger, webalizer, and AWstats can be installed as follows:
```
sudo apt-get install vlogger webalizer awstats geoip-database libclass-dbi-mysql-perl
```
Open /etc/cron.d/awstats afterwards...
```
sudo vim /etc/cron.d/awstats
```
... and comment out everything in that file:

#### Install Jailkit

Jailkit is needed only if you want to chroot SSH users. It can be installed as follows (important: Jailkit must be installed before ISPConfig - it cannot be installed afterwards!):
```
sudo apt-get install build-essential autoconf automake1.9 libtool flex bison debhelper binutils-gold
cd /tmp
wget http://olivier.sessink.nl/jailkit/jailkit-2.15.tar.gz
tar xvfz jailkit-2.15.tar.gz
cd jailkit-2.15
sudo ./debian/rules binary
```
You can now install the Jailkit .deb package as follows:
```
cd ..
sudo dpkg -i jailkit_2.15-1_*.deb
sudo rm -rf jailkit-2.15*
```

Thats it. Now jailkit is installed. Jailkit makes many commands available which can be used to setup chroot based jailed environments. Here are the commands
```
jk_addjailuser   jk_chrootlaunch  jk_cp            jk_jailuser      jk_lsh           jk_uchroot       
jk_check         jk_chrootsh      jk_init          jk_list          jk_socketd       jk_update
```

### fail2ban
This is optional but recommended, because the ISPConfig monitor tries to show the log:
```
sudo apt-get install fail2ban
```
To make fail2ban monitor PureFTPd and Dovecot, create the file /etc/fail2ban/jail.local:
```
sudo vim /etc/fail2ban/jail.local
```
```
[proftpd]
enabled  = true
port     = ftp
filter   = proftpd
logpath  = /var/log/syslog
maxretry = 3
```
```
[dovecot-pop3imap]
enabled = true
filter = dovecot-pop3imap
action = iptables-multiport[name=dovecot-pop3imap, port="pop3,pop3s,imap,imaps", protocol=tcp]
logpath = /var/log/mail.log
maxretry = 5
```
```
[postfix-sasl]
enabled  = true
port     = smtp
filter   = postfix-sasl
logpath  = /var/log/mail.log
maxretry = 3
```
Then create the following two filter files:
```
sudo vim /etc/fail2ban/filter.d/dovecot-pop3imap.conf
```
```
[Definition]
failregex = (?: pop3-login|imap-login): .*(?:Authentication failure|Aborted login \(auth failed|Aborted login \(tried to use disabled|Disconnected \(auth failed|Aborted login \(\d+ authentication attempts).*rip=(?P<host>\S*),.*
ignoreregex =
```
Add the missing ignoreregex line in the postfix-sasl file:
```
echo "ignoreregex =" >> sudo /etc/fail2ban/filter.d/postfix-sasl.conf
```
Restart fail2ban afterwards:
```
sudo service fail2ban restart
```

### Install SquirrelMail

To install the SquirrelMail webmail client, run
```
sudo apt-get install squirrelmail
```
Then configure SquirrelMail:
```
sudo squirrelmail-configure
```
We must tell SquirrelMail that we are using Dovecot-IMAP/-POP3:
```
SquirrelMail Configuration : Read: config.php (1.4.0)
---------------------------------------------------------
Main Menu --
1.  Organization Preferences
2.  Server Settings
3.  Folder Defaults
4.  General Options
5.  Themes
6.  Address Books
7.  Message of the Day (MOTD)
8.  Plugins
9.  Database
10. Languages

D.  Set pre-defined settings for specific IMAP servers

C   Turn color on
S   Save data
Q   Quit

Command >> <-- D


SquirrelMail Configuration : Read: config.php
---------------------------------------------------------
While we have been building SquirrelMail, we have discovered some
preferences that work better with some servers that don't work so
well with others.  If you select your IMAP server, this option will
set some pre-defined settings for that server.

Please note that you will still need to go through and make sure
everything is correct.  This does not change everything.  There are
only a few settings that this will change.

Please select your IMAP server:
    bincimap    = Binc IMAP server
    courier     = Courier IMAP server
    cyrus       = Cyrus IMAP server
    dovecot     = Dovecot Secure IMAP server
    exchange    = Microsoft Exchange IMAP server
    hmailserver = hMailServer
    macosx      = Mac OS X Mailserver
    mercury32   = Mercury/32
    uw          = University of Washington's IMAP server
    gmail       = IMAP access to Google mail (Gmail) accounts

    quit        = Do not change anything
Command >> <-- dovecot


SquirrelMail Configuration : Read: config.php
---------------------------------------------------------
While we have been building SquirrelMail, we have discovered some
preferences that work better with some servers that don't work so
well with others.  If you select your IMAP server, this option will
set some pre-defined settings for that server.

Please note that you will still need to go through and make sure
everything is correct.  This does not change everything.  There are
only a few settings that this will change.

Please select your IMAP server:
    bincimap    = Binc IMAP server
    courier     = Courier IMAP server
    cyrus       = Cyrus IMAP server
    dovecot     = Dovecot Secure IMAP server
    exchange    = Microsoft Exchange IMAP server
    hmailserver = hMailServer
    macosx      = Mac OS X Mailserver
    mercury32   = Mercury/32
    uw          = University of Washington's IMAP server
    gmail       = IMAP access to Google mail (Gmail) accounts

    quit        = Do not change anything
Command >> dovecot

              imap_server_type = dovecot
         default_folder_prefix = <none>
                  trash_folder = Trash
                   sent_folder = Sent
                  draft_folder = Drafts
            show_prefix_option = false
          default_sub_of_inbox = false
show_contain_subfolders_option = false
            optional_delimiter = detect
                 delete_folder = false

Press enter to continue... <-- ENTER


SquirrelMail Configuration : Read: config.php (1.4.0)
---------------------------------------------------------
Main Menu --
1.  Organization Preferences
2.  Server Settings
3.  Folder Defaults
4.  General Options
5.  Themes
6.  Address Books
7.  Message of the Day (MOTD)
8.  Plugins
9.  Database
10. Languages

D.  Set pre-defined settings for specific IMAP servers

C   Turn color on
S   Save data
Q   Quit

Command >> <-- S


SquirrelMail Configuration : Read: config.php (1.4.0)
---------------------------------------------------------
Main Menu --
1.  Organization Preferences
2.  Server Settings
3.  Folder Defaults
4.  General Options
5.  Themes
6.  Address Books
7.  Message of the Day (MOTD)
8.  Plugins
9.  Database
10. Languages

D.  Set pre-defined settings for specific IMAP servers

C   Turn color on
S   Save data
Q   Quit

Command >> <-- Q
```
You can now find SquirrelMail in the /usr/share/squirrelmail/ directory.

After you have installed ISPConfig 3, you can access SquirrelMail as follows:
The ISPConfig apps vhost on port 8081 for nginx comes with a SquirrelMail configuration, so you can use *http://server1.example.com:8081/squirrelmail* or *http://server1.example.com:8081/webmail* to access SquirrelMail.

If you want to use a /webmail or /squirrelmail alias that you can use from your web sites, this is a bit more complicated than for Apache because nginx does not have global aliases (i.e., aliases that can be defined for all vhosts). Therefore you have to define these aliases for each vhost from which you want to access SquirrelMail.

To do this, paste the following into the nginx Directives field on the Options tab of the web site in ISPConfig:
```
        location /squirrelmail {
               root /usr/share/;
               index index.php index.html index.htm;
               location ~ ^/squirrelmail/(.+\.php)$ {
                       try_files $uri =404;
                       root /usr/share/;
                       fastcgi_pass unix:/var/run/php5-fpm.sock;
                       fastcgi_index index.php;
                       fastcgi_param SCRIPT_FILENAME $request_filename;
                       include /etc/nginx/fastcgi_params;
                       fastcgi_param PATH_INFO $fastcgi_script_name;
                       fastcgi_buffer_size 128k;
                       fastcgi_buffers 256 4k;
                       fastcgi_busy_buffers_size 256k;
                       fastcgi_temp_file_write_size 256k;
                       fastcgi_intercept_errors on;
               }
               location ~* ^/squirrelmail/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                       root /usr/share/;
               }
        }
        location /webmail {
               rewrite ^/* /squirrelmail last;
        }
```
If you use https instead of http for your vhost, you should add the line fastcgi_param HTTPS on; to your SquirrelMail configuration like this:
```
        location /squirrelmail {
               root /usr/share/;
               index index.php index.html index.htm;
               location ~ ^/squirrelmail/(.+\.php)$ {
                       try_files $uri =404;
                       root /usr/share/;
                       fastcgi_pass unix:/var/run/php5-fpm.sock;
                       fastcgi_param HTTPS on; # <-- add this line
                       fastcgi_index index.php;
                       fastcgi_param SCRIPT_FILENAME $request_filename;
                       include /etc/nginx/fastcgi_params;
                       fastcgi_param PATH_INFO $fastcgi_script_name;
                       fastcgi_buffer_size 128k;
                       fastcgi_buffers 256 4k;
                       fastcgi_busy_buffers_size 256k;
                       fastcgi_temp_file_write_size 256k;
                       fastcgi_intercept_errors on;
               }
               location ~* ^/squirrelmail/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                       root /usr/share/;
               }
        }
        location /webmail {
               rewrite ^/* /squirrelmail last;
        }
```
If you use both http and https for your vhost, you need to add the following section to the http {} section in /etc/nginx/nginx.conf (before any include lines) which determines if the visitor uses http or https and sets the $fastcgi_https variable (which we will use in our SquirrelMail configuration) accordingly:
```
sudo vim /etc/nginx/nginx.conf
```
```
[...]
http {
[...]
        ## Detect when HTTPS is used
        map $scheme $fastcgi_https {
          default off;
          https on;

        }
[...]
}
[...]
```
Don't forget to reload nginx afterwards:
```
sudo service nginx reload
```
Then go to the nginx Directives field again, and instead of fastcgi_param HTTPS on; you add the line fastcgi_param HTTPS $fastcgi_https; so that you can use SquirrelMail for both http and https requests:
```
        location /squirrelmail {
               root /usr/share/;
               index index.php index.html index.htm;
               location ~ ^/squirrelmail/(.+\.php)$ {
                       try_files $uri =404;
                       root /usr/share/;
                       fastcgi_pass unix:/var/run/php5-fpm.sock;
                       fastcgi_param HTTPS $fastcgi_https; # <-- add this line
                       fastcgi_index index.php;
                       fastcgi_param SCRIPT_FILENAME $request_filename;
                       include /etc/nginx/fastcgi_params;
                       fastcgi_param PATH_INFO $fastcgi_script_name;
                       fastcgi_buffer_size 128k;
                       fastcgi_buffers 256 4k;
                       fastcgi_busy_buffers_size 256k;
                       fastcgi_temp_file_write_size 256k;
                       fastcgi_intercept_errors on;
               }
               location ~* ^/squirrelmail/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
                       root /usr/share/;
               }
        }
        location /webmail {
               rewrite ^/* /squirrelmail last;
        }
```

### ISPConfig 3

Before you start the ISPConfig installation, make sure that Apache is stopped (if it is installed - it is possible that some of your installed packages have installed Apache as a dependency without you knowing). If Apache2 is already installed on the system, stop it now...
```
sudo service apache2 stop
```
... and remove Apache's system startup links:
```
sudo update-rc.d -f apache2 remove
```
Make sure that nginx is running:
```
sudo service nginx restart
```
(If you have both Apache and nginx installed, the installer asks you which one you want to use: Apache and nginx detected. Select server to use for ISPConfig: (apache,nginx) [apache]:
Type nginx. If only Apache or nginx are installed, this is automatically detected by the installer, and no question is asked.)
To install ISPConfig 3 from the latest released version, do this:
```
cd /tmp
wget http://www.ispconfig.org/downloads/ISPConfig-3-stable.tar.gz
tar xfz ISPConfig-3-stable.tar.gz
cd ispconfig3_install/install/
```

The next step is to run
```
sudo php -q install.php
```
This will start the ISPConfig 3 installer. The installer will configure all services like Postfix, SASL, Courier, etc. for you. A manual setup as required for ISPConfig 2 (perfect setup guides) is not necessary.

*__NOTE:__*
If you get an error like
```
Wrong SQL-mode. You should use NO_ENGINE_SUBSTITUTION. Add

    sql-mode="NO_ENGINE_SUBSTITUTION"

to the mysqld-section in your mysql-config and restart mysqld afterwards
```
The sql-mode should only contain "NO_ENGINE_SUBSTITUTION" NOTHING ELSE. So lets make it happen:
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf 
```
At the end of the file add 
```
...
sql-mode="NO_ENGINE_SUBSTITUTION"
```
Then.
```
sudo service mysql restart
```
Now you can continue the install!

```
root@server1:/tmp/ispconfig3_install/install# php -q install.php
PHP Deprecated:  Comments starting with '#' are deprecated in /etc/php5/cli/conf.d/ming.ini on line 1 in Unknown on line 0


--------------------------------------------------------------------------------
 _____ ___________   _____              __ _         ____
|_   _/  ___| ___ \ /  __ \            / _(_)       /__  \
  | | \ `--.| |_/ / | /  \/ ___  _ __ | |_ _  __ _    _/ /
  | |  `--. \  __/  | |    / _ \| '_ \|  _| |/ _` |  |_ |
 _| |_/\__/ / |     | \__/\ (_) | | | | | | | (_| | ___\ \
 \___/\____/\_|      \____/\___/|_| |_|_| |_|\__, | \____/
                                              __/ |
                                             |___/
--------------------------------------------------------------------------------


>> Initial configuration

Operating System: 14.04 UNKNOWN

    Following will be a few questions for primary configuration so be careful.
    Default values are in [brackets] and can be accepted with <ENTER>.
    Tap in "quit" (without the quotes) to stop the installer.


Select language (en,de) [en]: <-- ENTER

Installation mode (standard,expert) [standard]: <-- ENTER

Full qualified hostname (FQDN) of the server, eg server1.domain.tld  [server1.example.com]: <-- ENTER

MySQL server hostname [localhost]: <-- ENTER

MySQL root username [root]: <-- ENTER

MySQL root password []: <-- yourrootsqlpassword

MySQL database to create [dbispconfig]: <-- ENTER

MySQL charset [utf8]: <-- ENTER

Apache and nginx detected. Select server to use for ISPConfig: (apache,nginx) [apache]: <-- nginx

Generating a 4096 bit RSA private key
.................................................................................................................................................................................................................................................................................................................................................................................................................................................................++
............................................++
writing new private key to 'smtpd.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]: <-- ENTER
State or Province Name (full name) [Some-State]: <-- ENTER
Locality Name (eg, city) []: <-- ENTER
Organization Name (eg, company) [Internet Widgits Pty Ltd]: <-- ENTER
Organizational Unit Name (eg, section) []: <-- ENTER
Common Name (e.g. server FQDN or YOUR name) []: <-- ENTER
Email Address []: <-- ENTER
Configuring Jailkit
Configuring Dovecot
Configuring Spamassassin
Configuring Amavisd
Configuring Getmail
Configuring Pureftpd
Configuring BIND
Configuring nginx
Configuring Vlogger
Configuring Apps vhost
Configuring Bastille Firewall
Configuring Fail2ban
Installing ISPConfig
ISPConfig Port [8080]: <-- ENTER

Do you want a secure (SSL) connection to the ISPConfig web interface (y,n) [y]: <-- ENTER

Generating RSA private key, 4096 bit long modulus
...++
.................................................................................................++
e is 65537 (0x10001)
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]: <-- ENTER
State or Province Name (full name) [Some-State]: <-- ENTER
Locality Name (eg, city) []: <-- ENTER
Organization Name (eg, company) [Internet Widgits Pty Ltd]: <-- ENTER
Organizational Unit Name (eg, section) []: <-- ENTER
Common Name (e.g. server FQDN or YOUR name) []: <-- ENTER
Email Address []: <-- ENTER

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []: <-- ENTER
An optional company name []: <-- ENTER
writing RSA key
Configuring DBServer
Installing ISPConfig crontab
no crontab for root
no crontab for getmail
Restarting services ...
Rather than invoking init scripts through /etc/init.d, use the service(8)
utility, e.g. service mysql restart

Since the script you are attempting to invoke has been converted to an
Upstart job, you may also use the stop(8) and then start(8) utilities,
e.g. stop mysql ; start mysql. The restart(8) utility is also available.
mysql stop/waiting
mysql start/running, process 2783
 * Stopping Postfix Mail Transport Agent postfix
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
   ...done.
 * Starting Postfix Mail Transport Agent postfix
postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
/usr/sbin/postconf: warning: /etc/postfix/main.cf: undefined parameter: virtual_mailbox_limit_maps
   ...done.
Stopping amavisd: amavisd-new.
Starting amavisd: amavisd-new.
 * Stopping ClamAV daemon clamd
   ...done.
 * Starting ClamAV daemon clamd
   ...done.
Rather than invoking init scripts through /etc/init.d, use the service(8)
utility, e.g. service dovecot restart

Since the script you are attempting to invoke has been converted to an
Upstart job, you may also use the stop(8) and then start(8) utilities,
e.g. stop dovecot ; start dovecot. The restart(8) utility is also available.
dovecot stop/waiting
dovecot start/running, process 3929
 * Reloading PHP5 FastCGI Process Manager php5-fpm
   ...done.
 * Reloading nginx configuration nginx
   ...done.
Restarting ftp server: Running: /usr/sbin/pure-ftpd-mysql-virtualchroot -l mysql:/etc/pure-ftpd/db/mysql.conf -l pam -A -b -u 1000 -D -H -Y 1 -E -8 UTF-8 -O clf:/var/log/pure-ftpd/transfer.log -B
Installation completed.
root@server1:/tmp/ispconfig3_install/install#
```

The installer automatically configures all underlying services, so no manual configuration is needed.

You now also have the possibility to let the installer create an SSL vhost for the ISPConfig control panel, so that ISPConfig can be accessed using *https://* instead of *http://*. To achieve this, just press ENTER when you see this question: Do you want a secure (SSL) connection to the ISPConfig web interface (y,n) [y]:.

Afterwards you can access ISPConfig 3 under *http(s)://server1.example.com:8080/* or *http(s)://192.168.0.100:8080/* ( http or https depends on what you chose during installation). Log in with the username admin and the password admin (you should change the default password after your first login):


![Ispconfig](http://i.imgur.com/osjAyR3.png "Ispconfig")


### Roundcube

We can install RoundCube as follows:
```
sudo apt-get install roundcube roundcube-plugins roundcube-plugins-extra
```
You will see the following questions:
```
Configure database for roundcube with dbconfig-common? <-- Yes
Database type to be used by roundcube: <-- mysql
Password of the database's administrative user: <-- yourrootsqlpassword (the password of the MySQL root user) 
MySQL application password for roundcube: <-- roundcubesqlpassword
Password confirmation: <-- roundcubesqlpassword
```
This will create a MySQL database called roundcube with the MySQL user roundcube and the password roundcubesqlpassword.
Next go to your website in ISPConfig. On the Options tab, you will see the nginx Directives field:


Fill in the following directives and click on Save (it does not matter if you have PHP enabled for this vhost or not because this code snippet uses the system's default PHP which runs under the user and group www-data which is important because RoundCube is installed outside of the vhost's document root - in /var/lib/roundcube):
```
location /roundcube {
         root /var/lib/;
         index index.php index.html index.htm;
         location ~ (.+\.php)$ {
                    try_files $uri =404;
				   root  /var/lib/roundcube/;
				   fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
				   fastcgi_index index.php;
				   fastcgi_param SCRIPT_FILENAME $request_filename;
				   include /etc/nginx/fastcgi_params;
				   fastcgi_param PATH_INFO $fastcgi_script_name;
				   fastcgi_buffer_size 128k;
				   fastcgi_buffers 256 4k;
				   fastcgi_busy_buffers_size 256k;
				   fastcgi_temp_file_write_size 256k;
				   fastcgi_intercept_errors on;
         }
         location ~* /.svn/ {
                     deny all;
         }
         location ~* /README|INSTALL|LICENSE|SQL|bin|CHANGELOG$ {
                     deny all;
         }
}
location /webmail {
         rewrite ^ /roundcube last;
}
```
With this configuration, RoundCube will be accessible under the URLs http://www.example.com/webmail and http://www.example.com/roundcube.
### Configuring RoundCube

Open /etc/roundcube/main.inc.php...
```
sudo vim /etc/roundcube/main.inc.php
```
... and set $rcmail_config['default_host'] = 'localhost'; (or the hostname or IP address of your mail server if it is on a remote machine):
```
[...]
$rcmail_config['default_host'] = 'localhost';
[...]
```
Otherwise RoundCube will ask for a hostname before each login which might overstrain your users - we want to make usage as easy as possible.
Next install the ISPConfig 3 plugins for RoundCube:
```
cd /tmp
git clone https://github.com/w2c/ispconfig3_roundcube.git
cd /tmp/ispconfig3_roundcube/
sudo mv ispconfig3_* /usr/share/roundcube/
cd /usr/share/roundcube/
sudo mv ispconfig3_account/config/config.inc.php.dist ispconfig3_account/config/config.inc.php
sudo ln -s /usr/share/roundcube/ispconfig3_* /var/lib/roundcube/plugins/
```
Open ispconfig3_account/config/config.inc.php...
```
sudo vim ispconfig3_account/config/config.inc.php
```
... and fill in the login details of your ISPConfig remote user and the URL of the remote API - my ISPConfig installation runs on https://192.168.0.26:8080, so the URL of the remote API is https://192.168.0.26:8080/remote/:
```
<?php
$rcmail_config['identity_limit'] = false;
$rcmail_config['remote_soap_user'] = 'roundcube';
$rcmail_config['remote_soap_pass'] = 'Sw0wlytlRt3MY';
$rcmail_config['soap_url'] = 'https://192.168.0.26:8080/remote/';
?>
```
Finally open /etc/roundcube/main.inc.php again...
```
sudo vim /etc/roundcube/main.inc.php
```
... and enable the jquerui plugin plus the ISPConfig 3 plugins...
```
[...]
// ----------------------------------
// PLUGINS
// ----------------------------------

// List of active plugins (in plugins/ directory)
//$config['plugins'] = array(); <- __If you have something in this array include that as well__
$config['plugins'] = array("jqueryui", "ispconfig3_account", "ispconfig3_autoreply", "ispconfig3_pass", "ispconfig3_spam", "ispconfig3_fetchmail", "ispconfig3_filter");
[...]
```
... and change the skin from default to classic (otherwise the ISPConfig 3 plugins will not work):
```
[...]
// skin name: folder from skins/
$config['skin'] = 'classic';
[...]
```
THat's it. Now you can access roundcube as well.
### Make the services available from outside networks as well
My network hoster is UPC Hungary. I will show how to set the appropriate ports on their admin side. Yours should be similar too. 

Go to : http://192.168.0.1/ and login. NOTE: In UPC the default username and password is always 'admin'-'admin'. Adviced to change them! 

![upc](http://i.imgur.com/7TWcI4b.png "upc") 

After login you will be able to see your network details. 

Go to Általános(Basic) menu item and you should see your IP Address:
My IP address in this case was: 89.133.123.176
Since I do not bought a fix ip address service from UPC my ip address changes dynamicly. In this case the IP will change in 2016-12-07 16:15:26
Later we will fix the static ip address problem.
![upc](http://i.imgur.com/EJ5gexD.png"upc") 

These are the default ports services uses.
![ports](https://scontent-vie1-1.xx.fbcdn.net/v/t1.0-0/s526x395/14484771_1231763793532373_8029665007327781424_n.jpg?oh=a0b53f5c37e1eeb59269668bd903a5c5&oe=588FDBDE "ports") 


Right now the webserver is not available at all. If you go to http://89.133.123.176 you should not get the website of your webserver. This is becouse ports are not enabled for it.

So enable the port for the webserver. By default websites are on port 80.
Go to Advanced and then Forwarding submenu. There set up the following and save it!

![upc](http://i.imgur.com/wdhGRvP.png"upc") 

Now if you go to http://89.133.123.176 You should see your webserver working.

You can add your neccessary ports here as well. 









