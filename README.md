## Linux-server-configuration

This project requires you to access, secure, and perform the initial configuration of a bare-bones Linux server. It will also involve installing and configuring a web and database server and will host a web application.

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
1. Follow instructions to get a instance of server on Amazon Lightsail.
2. Follow instructions to SSH into the server.

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
