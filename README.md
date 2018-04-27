## Linux-server-configuration

This project requires you to access, secure, and perform the initial
configuration of a bare-bones Linux server.
It will also involve installing, configuring a web and database server, and
will host a web application.

* IP Address:
               18.220.198.31
* Public URL:                            
      http://ec2-18-220-198-31.us-east-2.compute.amazonaws.com

* SSH Port:

      2200

### Summary of Software installed and configuration changes made.

#### Software installed.
Hosting the application will require the Python virtual environment, Apache
with mod_wsgi, PostgreSQL, and Git.

    Amazon LightSail for publicly accessible Ubuntu Linux server.
    Apache Web server
    Postgresql database
    Python 2.7
    Item Catalog Web application developed in Project 3.

#### Secure your server.
1. Follow instructions in project to get a instance of server on Amazon
   Lightsail.
2. Follow instructions in project to SSH into the server.

##### Update all currently installed packages.

```
1. $ sudo apt-get update.
2. $ sudo apt-get upgrade.
3. Install finger, $ apt-get install finger.
```

Change the SSH port from 22 to 2200. Make sure to configure the Lightsail
firewall to allow it.
1. On Amazon Lightsail, open the Networking tab for the instance.
   Add custom port 2200 and 123
2. Download your private key from Amazon Lightsail.
   It was called, LightsailDefaultPrivateKey-us-east-2.pem
3. SSH this time with,
**ssh -i LightsailDefaultPrivateKey-us-east-2.pem ubuntu@18.220.198.31 -p 2200**


Configure the Uncomplicated Firewall (UFW) to only allow incoming
connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
```
1. $ sudo ufw allow 2200/tcp.
2. $ sudo ufw allow 80/tcp.
3. $ sudo ufw allow 123/udp.
4. $ sudo ufw enable
```
##### Give Grader access to the server
  1. Create user grader.

      **$ sudo adduser grader**

  It will ask for password and then a few other fields which you can be left
  blank.

  2. Give grader permission to sudo.

        **$ sudo nano /etc/sudoers.d/grader.**

  This will create a new file that will be the superuser configuration for
  grader.
  Type grader ALL=(ALL:ALL) NOPASSWD:ALL

  3. Create an SSH key pair for grader using the ssh-keygen tool.

    * From a new terminal run the command:
     **$ ssh-keygen -f ~/.ssh/udacity.rsa**

    * In the same terminal we need to read and copy the public key using
    the command: **$ cat ~/.ssh/udacity.rsa.pub**.
    Copy the key from the terminal.

    * Back in the server terminal locate the folder for the user grader,
      it should be /home/grader. Run the command **$ cd /home/grader**
      to move to the folder.

   * Create a directory called .ssh with the command **$ mkdir .ssh**

   * Create a file to store the public key with the command
     **$ touch .ssh/authorized_keys**

   * Edit that file using **$ nano .ssh/authorized_keys**
     Now paste in the public key

     We must change the permissions of the file and its folder by running
     **$ sudo chmod 700 /home/grader/.ssh**

     **$ sudo chmod 644 /home/grader/.ssh/authorized_keys**

   * Change the owner of the .ssh directory from root to grader by using the
     command **$ sudo chown -R grader:grader /home/grader/.ssh**

   * The last thing we need to do for the SSH configuration is restart its
     service with **$ sudo  service ssh restart_**

   * Disconnect from the server.

   * Now we need to login with the grader account using ssh. From your local  
     terminal type **$ ssh -i ~/.ssh/udacity.rsa grader@18.188.169.39**
     You should now be logged into your server via SSH

   * Lets enforce key authentication from the ssh configuration file by
     editing **$ sudo nano /etc/ssh/sshd_config**.
     Find the line that says PasswordAuthentication and change it to no.
     Also find the line that says Port 22 and change it to Port 2200.
     Lastly change PermitRootLogin to no.

   * Restart ssh again: **$ sudo service ssh restart**
   * Disconnect from the server.
   * Connect again with new port.
     **$ ssh -i ~/.ssh/udacity.rsa grader@18.220.198.31 -p 2200**


##### Prepare to deploy your project.
  * Configure time zone to UTC. **$ sudo dpkg-reconfigure tzdata**
  * Install and configure Apache to serve a Python mod_wsgi application.
  **$ sudo apt-get install apache2

  $ sudo apt-get install libapache2-mod-wsgi python-dev**

  Enable mod_wsgi with the command **$ sudo a2enmod wsgi**
  and restart Apache using **$ sudo service apache2 restart.**

##### Install and configure PostgreSQL

```
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
```
##### Install git.
  **sudo apt-get install git**

##### Clone and setup your Item Catalog project from the Github repository.

    **git clone <repo url>**
```
    $ cd /var/www
    $ sudo mkdir catalog
    $ sudo chown -R grader:grader catalog
    $ cd catalog
```
    In this directory we will have our catalog.wsgi file
    var/www/catalog/catalog.wsgi.
    Our virtual environment directory which we will create soon and call
    venv /var/www/catalog/venv, and also our application which will sit
    inside of another directory called catalog /var/www/catalog/catalog.

    Create the .wsgi file by **$ sudo nano catalog.wsgi**

    ```
    #!/usr/bin/python
    import sys
    import logging
    import os

    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/catalog/catalog")

    from catalog import app as application
    application.secret_key = os.urandom(24)

    ```
 Rename application.py to catalog.py $ mv application.py catalog.py
 Provide full path to client_secrets.json where called in application.

 Edit client_secrets.json to provide the correct IP address.

 Go into console.developers.google.com to add the new urls for redirect.

 Edit catalog.py last line app.run(host='18.220.198.31', port=80)
 to point to your host.

 Configure the virtual environment.
 Make sure you are in /var/www/catalog.
   ```
  $ sudo pip install virtualenv
  $ sudo virtualenv venv
  $ source venv/bin/activate
  $ sudo chmod -R 777 venv
  ```
 Command line will change with (venv) prepended to current command line.

 While our virtual environment is activated we need to install all packages
 required for our Flask application.
  ```
  $ sudo apt-get install python-pip
  $ sudo pip install flask
  $ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 #etc...
  ```

Configure the virtual environment to run the application.

**$ sudo nano /etc/apache2/sites-available/catalog.conf**
```
<VirtualHost *:80>
    ServerName 18.220.198.31
    ServerAlias ec2-18-220-198-31.us-east-2.compute.amazonaws.com
    ServerAdmin admin@18.220.198.31
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog /var/log/apache2/error.log
    LogLevel warn
    CustomLog /var/log/apache2/access.log combined
</VirtualHost>
```
Save and quit nano

Enable to virtual host: **$ sudo a2ensite catalog.conf** and DISABLE the default
host $ a2dissite 000-default.conf otherwise your site will not load with the
hostname.

In python code change, engine from sqlite://catalog.db to postgresql://catalog:catalog@localhost:5432/catalog

Create tables and load data from catalog application.
Run, database_setup.py and add_catalog_items.py

Final step is, Restart your apache server **$ sudo service apache2 restart**
Application should then be available on the IP
http://ec2-18-220-198-31.us-east-2.compute.amazonaws.com


### Attributions.
The following websites were used for reference.
https://github.com/mulligan121/Udacity-Linux-Configuration/tree/1b7e0475723e824814ca9669e6b9ddb3f6f7d8eb

https://github.com/callforsky/udacity-linux-configuration

https://discussions.udacity.com/
