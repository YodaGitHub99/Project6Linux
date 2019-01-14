Udacity Project 6 - Linux Server Configuration
===============================================
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

GitHub Repository - https://github.com/YodaGitHub99/Project6Linux

IP Address = 54.88.202.15
SSH Port = 2200
URL - Catalog application = http://54.88.202.15.xip.io/

Software installed/updated - Following commands were run -
===========================================================
sudo apt-get update
Apache - sudo apt-get install apache2
Python mod_wsgi package - sudo apt-get install libapache2-mod-wsgi-py3
PostgreSQL - sudo apt-get install postgresql
GIT - sudo apt-get install git-core
finger - sudo apt install finger
Python PIP - sudo apt-get install python3-pip python3-dev nginx
            sudo apt install python-pip
Flask (needed for Catalog Webapp) - pip install flask
sqlalchemy (needed for Catalog Webapp) - pip install sqlalchemy
Google API Client (needed for Catalog Webapp) - pip install --upgrade google-api-python-client
Oauth2Client (needed for Catalog Webapp) - pip install --upgrade oauth2client
Upgrade software - sudo apt-get upgrade

Configuration changes -
========================
Change the SSH port from 22 to 2200 (changed in /etc/ssh/sshd_config)
Create user grader - sudo adduser grader
Give grader the permission to sudo - /etc/sudoers.d/grader
Create an SSH key pair for grader - ssh-keygen
Disable password-based login for user grader - set PasswordAuthentication to "no" in /etc/ssh/sshd_config
Configure the local timezone to UTC - sudo dpkg-reconfigure tzdata

Secure your server -
====================
Update all currently installed packages - sudo apt-get update
                                          sudo apt-get upgrade
Configure UFW -
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow ntp
sudo ufw enable

sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123                        ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123 (v6)                   ALLOW       Anywhere (v6)

Install web application on server -
====================================
Create directory FlaskApp in /var/www
Create wsgi file - flaskapp.wsgi in FlaskApp directory
Clone catalog codebase from GITHub - git clone https://YodaGitHub99@github.com/YodaGitHub99/catalog FlaskApp
Edit the wsgi file & add the following (as per Flask mod_wsgi recommendation - see resources below)

activate_this = '/var/www/FlaskApp/FlaskApp/venv/bin/activate_this.py'
execfile(activate_this, dict(__file__=activate_this))

#!/usr/bin python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/FlaskApp")

from FlaskApp import app as application
application.secret_key = 'super_secret_key'

Create Virtual machine environment (required for Flask app - see resources below) -
sudo pip install virtualenv
sudo virtualenv venv
source venv/bin/activate

In virtual env run following -
pip install Flask
pip install sqlalchemy
pip install requests
pip install oauth2client

Create file FlaskApp.conf in /etc/apache2/sites-enabled/
Add the following -

<VirtualHost *:80>
                ServerName 54.88.202.15.xip.io
                ServerAdmin webmaster@server.com
                ServerAlias ec2-54-88-202-15.compute-1.amazonaws.com
                WSGIDaemonProcess FlaskApp user=grader group=grader threads=5
                WSGIProcessGroup FlaskApp
                WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi

                <Directory /var/www/FlaskApp/FlaskApp/>
                        Order allow,deny
                        Allow from all
                WSGIProcessGroup FlaskApp
                WSGIApplicationGroup %{Global}
                </Directory>
                Alias /static /var/www/FlaskApp/FlaskApp/static
                <Directory /var/www/FlaskApp/FlaskApp/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

Modify Google OAUTH -
=======================
Edit Credentials -
Add - http://54.88.202.15.xip.io to Authorized JavaScript origins
Add - http://54.88.202.15.xip.io/login & http://54.88.202.15.xip.io/gconnect to Authorized redirect URIs

Please note that Facebook login does not work (requires https). Only Google login was tested

postgresql Database -
======================
CREATE USER catalog WITH PASSWORD 'catalog_pwd';
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;

To create the database tables - run database_setup.py -
    python database_setup.py
To pre-populated the db with some data - run test_data.py -
    python test_data.py

Restart Apache2 - sudo /etc/init.d/apache2 restart

Resources -
=============
Amazon Lightsail for hosting Ubuntu instance & web application
Udacity course videos
Stackoverflow
AskUbuntu.com
Flask (mod_wsgi) - http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/
Virtual env for flask - http://flask.pocoo.org/docs/1.0/installation/
Postgresql db related activities - https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e
