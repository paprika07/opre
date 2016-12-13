# Operating Systems Home Project - Topic 4.
Ferencz Dávid Tamás (ep7d0o) - Láda Bence(R1DDDP)
##### NOTES
  - We installed ubuntu server (more specificly the minimal version from the raspberry pi official site) on a raspberry pi 3 model b
  - Some of the software we install is not listed, but for our purposes we must install and configure them. 
  - The raspberry is David's proprerty and he also owns the domain name that we will use.
  - We will be using softwares, which requires usernames and passwords. Its a good idea to make a file for you, which will contains all username-passwords.

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
PHP-FPM is a daemon process (with the init script php7.0-fpm) that runs a FastCGI server on the socket /run/php/php7.0-fpm.sock.