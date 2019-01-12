Udacity Project 6 - Linux Server Configuration
===============================================
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

GitHub Repository - https://github.com/YodaGitHub99/Project6Linux

IP Address = 54.88.202.15
SSH Port = 2200
URL - Catalog application = 

Software installed/updated - Following commands were run -
===========================================================
sudo apt-get update
Apache - sudo apt-get install apache2
Python 3 mod_wsgi package - sudo apt-get install libapache2-mod-wsgi-py3
PostgreSQL - sudo apt-get install postgresql
SQLLite - sudo apt-get install sqlite3 libsqlite3-dev
GIT - sudo apt-get install git-core
finger - sudo apt install finger
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


====================================================================

Please follow these steps to properly submit this project:

DONE    Create a new GitHub repository and add a file named README.md.

Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.

Locate the SSH key you created for the grader user.

During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.
