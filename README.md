## Linux-server-configuration

This project requires you to access, secure, and perform the initial configuration of a bare-bones Linux server. It will also involve installing, configuring a web and database server, and will host a web application.

* IP Address:
               18.220.198.31
* Public URL:                            
      http://ec2-18-220-198-31.us-east-2.compute.amazonaws.com

* SSH Port:

      2200

### Summary of Software installed and configuration changes made.

#### Software installed.
Amazon LightSail for publicly accessible Ubuntu Linux server.
Apache Web server
Postgresql database
Python 2.7
Item Catalog Web application developed in Project 3.

#### Secure your server.
1. Follow instructions in project to get a instance of server on Amazon Lightsail.
2. Follow instructions in project to SSH into the server.

##### Update all currently installed packages.
1. $ sudo apt-get update.
2. $ sudo apt-get upgrade.
3. Install finger, $ apt-get install finger.


##### Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
1. On Amazon Lightsail, open the Networking tab for the instance.
Add custom port 2200 and 123
2. Download your private key from Amazon Lightsail.
It was called, LightsailDefaultPrivateKey-us-east-2.pem
3. SSH this time with, ssh -i LightsailDefaultPrivateKey-us-east-2.pem ubuntu@18.220.198.31 -p 2200


##### Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
1. $ sudo ufw allow 2200/tcp.
2. $ sudo ufw allow 80/tcp.
3. $ sudo ufw allow 123/udp.
4. $ sudo ufw enable

##### Give Grader access to the server
1. Create user grader.

        $ sudo adduser grader

  It will ask for password and then a few other fields which you can leave blank.

2. Give grader permission to sudo.

        $ sudo nano /etc/sudoers.d/grader.

  This will create a new file that will be the superuser configuration for grader. When nano opens type grader ALL=(ALL:ALL), to save the file hit Ctrl-X on your keyboard, type 'Y' to save, and return to save the filename.
3. Create an SSH key pair for grader using the ssh-keygen tool.

  * From a new terminal run the command: **$ ssh-keygen -f ~/.ssh/udacity.rsa**

  * In the same terminal we need to read and copy the public key using the command: **$ cat ~/.ssh/udacity.rsa.pub**. Copy the key from the terminal.

  * Back in the server terminal locate the folder for the user grader, it should be /home/grader. Run the command **$ cd /home/grader** to move to the folder.

  * Create a directory called .ssh with the command **$ mkdir .ssh**

  * Create a file to store the public key with the command **$ touch .ssh/authorized_keys**

  * Edit that file using **$ nano .ssh/authorized_keys**
   Now paste in the public key

   We must change the permissions of the file and its folder by running
   **$ sudo chmod 700 /home/grader/.ssh**

   ** $ sudo chmod 644 /home/grader/.ssh/authorized_keys**

   * Change the owner of the .ssh directory from root to grader by using the command $ sudo chown -R grader:grader /home/grader/.ssh

   * The last thing we need to do for the SSH configuration is restart its service with **$ sudo service ssh restart_**

   * Disconnect from the server.

   * Now we need to login with the grader account using ssh. From your local  terminal type **$ ssh -i ~/.ssh/udacity.rsa grader@18.188.169.39**
     You should now be logged into your server via SSH

   * Lets enforce key authentication from the ssh configuration file by   editing **$ sudo nano /etc/ssh/sshd_config**. Find the line that says PasswordAuthentication and change it to no. Also find the line that says Port 22 and change it to Port 2200. Lastly change PermitRootLogin to no.

  * Restart ssh again: **$ sudo service ssh restart**
  * Disconnect from the server.
  * Connect again with new port.
  **$ ssh -i ~/.ssh/udacity.rsa grader@18.220.198.31 -p 2200**


4. Prepare to deploy your project.
  * Configure time zone to UTC. **$ sudo dpkg-reconfigure tzdata**
  * Install and configure Apache to serve a Python mod_wsgi application.
  **$ sudo apt-get install apache2

  $ sudo apt-get install libapache2-mod-wsgi python-dev**

  Enable mod_wsgi with the command **$ sudo a2enmod wsgi** and restart Apache using **$ sudo service apache2 restart.**

5. Install and configure PostgreSQL

  $ sudo apt-get install libpq-dev python-dev

  $ sudo apt-get install postgresql postgresql-contrib

  $ sudo su - postgres -i

  $ psql

  postgres=# CREATE USER catalog WITH PASSWORD [password];
  postgres=# ALTER USER catalog CREATEDB;
  postgres=# CREATE DATABASE catalog with OWNER catalog;
  postgres=# \c catalog
  catalog=# REVOKE ALL ON SCHEMA public FROM public;
  catalog=# GRANT ALL ON SCHEMA public TO catalog;
  catalog=# \q
  $ exit

6. Install git.
  **sudo apt-get install git**

7. Clone and setup your Item Catalog project from the Github repository.
