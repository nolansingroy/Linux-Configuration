# Linux Configuration

# Purpose
The main purpose of this project is to configure virtual machine to host
your web sites or applications, set up security measures on the system, and manage users.
Here we used Amazon Light Sail due to it being one of the cheapest virtual machine available.

# Description
Here I was able to take a python flask application and get it running on this light sail ubuntu instance serving the application using apache2.
Some of the technologies used were PostgreSQL, Flask, Apache2, mod_wsgi, sudo, finger.  
# IP  Address
```
http://ec2-54-212-242-59.us-west-2.compute.amazonaws.com/
```
# SSH Port
```
Port 2200
```
# System Update
```
$ sudo apt-get update
$ sudo apt-get upgrade
```

# Disabling Port 22
First make sure you have port 2200  under custom tab within the Amazon Light Sail Developer Console
else you will get locked out.
 Now Change the default port 22 to 2200 in file
```
$ sudo nano /ect/ssh/sshd_config
```
The rest of the commands may now be used from a terminal loggin session, since the amazon web session console used port 22
to access the ubuntu instance

# Adding a User
* note you may install the program finger ```sudo apt-get install  finger``` to check the status
of your user calling ``` finger <user> ``` at any time!
```
sudo useradd grader
```
now since we are using Amazon light sail we may download a new
ssh key for our user 'grader' and give the file strict permissions
```
chmod 400 grader-keypair.pem
```
To retrieve the key
```
ssh-keygen -y
```
Enter the location of the file and press Enter. The Terminal should now display
the key in which you should copy to be placed in our authorized_keys file on the
amazon instance running as user grader.

```
mkdir .ssh
chmod 700 .ssh
cd .ssh
touch authorized_keys
chmod 600 authorized_keys
nano authorized_keys
```
Paste the key contents into the nano editor of authorized_keys and save!
Now you should be able to login using the ssh keys to the user grader.
```
sudo ssh -i grader.pem grader@54.212.242.59 -p 2200
```

# Managing Users
Give the new user sudo by adding them to the sudoer.d group
As root user run
```
$ nano /etc/sudoers
```
Now add grader to the file, here you should also see another user that
the amazon light sail created for you as the time of setting up the instace
mines was called 'ubuntu'

```
# User privilege specification
root    ALL=(ALL:ALL) ALL
grader    ALL=(ALL:ALL) ALL
```

# Disabling Root Login
From the file /etc/ssh/sshd_config

Change line ``` PermitRootLogin without-password ```
to ``` PermitRootLogin no```
We should also uncomment and set this line from yes to no
```
PasswordAuthentication no
```
Run ``` service ssh restart```
for the effects to take place

# Configuration of the Uncomplicated Firewall (UFW)
By default ufw is inactive therefore to ensure security we must
configure it.

Block all incoming connections: ``` sudo ufw default deny incoming ```

Allow outgoing connections on all ports: ``` sudo ufw default allow outgoing ```

Allow connections for ssh/2200 , http/80 ntp/123
```
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow ntp
```
To check these rules: ``` sudo ufw show added ```

To enable the rules ``` sudo ufw enable ```

Finally, check that the firewall is active by:
```
sudo ufw status
```

# Set the local timezone to UTC
```
sudo dpkg-reconfigure tzdata
```
# Installing Dependencies
Here were going to install Flask, SQLAlchemy, and PostgreSQL
, Apache2, and mod_wsgi
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi python-dev
sudo apt-get install postgresql postgresql-contrib
```
# Setup up PostSQL database
Create the user named catalog
```
sudo -u postgre createuser -P catalog
```
Create an empty database named catalog under the user catalog
```
sudo -u postgres createdb -O catalog catalog
```
* note that you will be prompted to give your user a password


# Configuring your Apache2 served application

# Set up git
```
git config --global user.name <username>
git config --global user.email <email>
```
# Enable Apache2
We may also stop and restart apache2
please read the man docs for more commands

```
sudo service apache2 start
```
# mod_wsgi:
```
sudo a2enmod wsgi
```
# Clone your app into the apache2 default served site directory
We also used the 'chown' command to change the owner of the folder
```
cd /var/www
sudo mkdir fullstack
sudo chown www-data:www-data fullstack/
cd fullstack
sudo -u www-data git clone https://github.com/nolansingroy/catalog.git fullstack
```
# Protect git
```
sudo chmod 700 /var/www/fullstack/catalog/.git
```
Next we need to create a catalog.wsgi file to serve the application
this file should be located right outside your cloned project folder which in this case is the folder catalog.
Thes structure should look like these
```
/var/www/fullstack/
catalog catalog.wsgi
```
Within this catalog.wsgi file we also need to specify the app secret
and postgresql username password and database
it should look like this

```
#!/usr/bin/python
import sys
import logging
loggin.basicConfig(stream=sys.stderr)
sys.path.insert(0, '/www/fullstack/catalog/catalog')

from catalog import app as application
application.sectret_key = 'secret_key'

application.config['DATABASE_URL'] = 'postgresql://catalog:password@localhost/catalog'
```
* note that within your project's create_database.py and populate_database.py script you
will need to change the old data base path and name to the new postgresql one
then call
```
python create_database.py
python populate_database.py
```
Another way more efficent way instead of manually creating the database
would be to just import the modules and call them at the very end of our catalog.wsgi file
```
 right under import app as application line
from catalog.create_database import create_db

 then at the very under our application.config lines
create_db(application.config['DATABASE_URL'])
```

# Reconnect third party Authentication Services
We may do these by filling in the client_secret.json file
and the google developer api credential, specifically the javascript_origins
section to match that of your amazon dynamic or static hosted instance ip address
mine was
```
http://ec2-54-212-242-59.us-west-2.compute.amazonaws.com
```
* also note that amazon only allows the call back to the full ip address as stated above
if you simply try to call the http://54.212.242.59 you will retrieve a 400 error
```


# Apache2 configuration

Create the virutal host file within
``` /etc/apache2/sites-available/catalog.conf ```

and give it a virtual host script as follows


```
<VirtualHost *:80>
                ServerName 54.212.242.59
                ServerAdmin nolansingroy@gmail.com
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog/catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalog/static
                <Directory /var/www/catalog/catalog/static/>
                        DirectoryIndex /notfound
                        FallbackResource /notfound
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
 </VirtualHost>
```

* Please note that ``` Order allow,deny and Allow from all ```
may be changed in to one single line of ``` Required all granted ```
due to depreciation of the code.


# Set the apache2 served site to serve your project

1. Disable default virtual host: ``` sudo a2dissite 000-default.conf ```
2. Enable your application virtual host: ``` sudo a2ensite catalog.conf ```
3. Reload Apache2 ``` sudo service apache2 reload ```
Go visit your full domain url ``` http://ec2-54-212-242-59.us-west-2.compute.amazonaws.com/ ```
* Remember if you need to debug you may just change the url endings
to what ever url you routed to when building your app within Flask, to visit each
individual page of your application.

# Helpful Documentations

1. https://aws.amazon.com/premiumsupport/knowledge-center/new-user-accounts-linux-instance/
2. http://postgresguide.com/setup/users.html
3. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
