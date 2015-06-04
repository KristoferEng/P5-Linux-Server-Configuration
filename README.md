# P5-Linux-Server-Configuration

The Linux Server Configuration Project is the fifth project in the Udacity Full Stack Nanodegree. The goal was to fully configure the server to host the Item Catalog Application.

To access the website running the Item Catalog application please visit: http://www.slikqaz.tk/

To access the Item Catalog files please visit: https://github.com/slikqaz/catalog

To ssh access the server, use the following: ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@52.11.160.171
Be sure to include the RSA key in the submission note at ~/.ssh/udacity_key.rsa.

# Configuration
## 1. Launch your Virtual Machine with your Udacity account
- Create development environment from https://www.udacity.com/account#!/development_environment

## 2. Follow the instructions provided to SSH into your server
- Download private key
- In terminal, mv ~/Downloads/udacity_key.rsa ~/.ssh/
- In terminal, chmod 600 ~/.ssh/udacity_key.rsa
- Log into SSH using ssh -i ~/.ssh/udacity_key.rsa -p 2200 root@52.11.160.171
## 3. Create a new user named grader
- In ssh terminal, adduser grader
- Set password
4. Give the grader the permission to sudo
- In ssh terminal, adduser grader sudo
5. Update all currently installed packages
- For list of packages: in ssh terminal, apt-get update
- To update: in ssh terminal, sudo apt-get upgrade
6. Change the SSH port from 22 to 2200
- In ssh terminal, nano /etc/ssh/sshd_config
- Change port 22 to 2200
7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
- In ssh terminal, ufw allow 2200/ssh
- In ssh terminal, ufw allow 80/http
- In ssh terminal, ufw allow 123/ntp
8. Configure the local timezone to UTC
- In ssh terminal, dpkg-reconfigure tzdata
9. Install and configure Apache to serve a Python mod_wsgi application
- In ssh terminal, apt-get install apache2
- In ssh terminal, apt-get install python-setuptools libapache2-mod-wsgi
10. Install and configure PostgreSQL:
- In ssh terminal, apt-get install postgresql
- In ssh terminal, su - postgres
- In ssh terminal, psql
- In ssh terminal, create database catalog
10.a Do not allow remote connections
- Postgres does this by default
10.b Create a new user named catalog that has limited permissions to your catalog application database
- In ssh terminal, create user catalog -l
11. Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!
- Set up file structure with the following:
  /var/www/catalog/catalog where the second catalog is where the catalog app files go
  /var/www/catalog/catalog.wsgi see below for code
  /etc/apache2/sites-available/catalog.conf see below for code (replace with your own website)
- In ssh terminal, apt-get install python-pip
- In ssh terminal, pip install virtualenv 
- In ssh terminal, virtualenv venv
- In ssh terminal, source venv/bin/activate 
- In ssh terminal, pip install Flask
- In ssh terminal, pip install SQLAlchemy
- In ssh terminal, apt-get install python-sqlalchemy
- In ssh terminal, pip install oauth2 (also may need pip install --upgrade oauth2client and pip install --upgrade google-api-python-client)
- In ssh terminal, a2ensite catalog
- In ssh terminal, service apache2 restart
- In browser, the website should show up on your domain at this point.
12. Enable SSH for grader.
- In ssh terminal, su - grader
- In ssh terminal, mkdir .ssh
- In ssh terminal, chmod 700 .ssh
- In ssh terminal, touch .ssh/authorized_keys
- In ssh terminal, chmod 600 .ssh/authorized_keys
- In ssh terminal, take the public key from the root in authorized_keys and place it in the grader's authorized_keys
13. SSH into grader
- In terminal, ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@52.11.160.171


Resources
LAMP - http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html
Flask App Config - https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
SQL Alchemy - pip install SQLAlchemy
Oatuh2 - install oauth2

# Catalog.wsgi
  #!/usr/bin/python
  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0,"/var/www/catalog/")

  from catalog import app as application
  application.secret_key = 'super_secret_key'

# catalog.conf
  <VirtualHost *:80>
                ServerName slikqaz.tk
                ServerAlias www.slikqaz.tk
                ServerAdmin admin@mywebsite.com
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
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>










Additional Installs (For reference for myself)
apt-get install python-sqlalchemy
pip install oauth2
pip install --upgrade oauth2client
