# linux-server-configuration
i take a baseline installation of a Linux server and prepare it to host my web applications. and i secure myserver from a number of attack vectors, install and configure a database server, and deploy one of my existing web applications onto it. catalog item

ip address:3.8.236.91
ssh port: 2200
complete url http://3.8.236.91

steps:
1-logged into lightsail
2-download lightsail.pem
3-in the local machine create .ssh
4-make a file lightsail bu touch .ssh/lightsail.rsa
5-edit lightsail and put a copy of the content of lightsail.pem using nano .ssh/lightsail.rsa
6-make permission chmod 700 .ssh
7- chmod 600 .ssh/lightsail.rsa
8-log into to the server from local machine using 
  ssh ubuntu@3.8.236.91 -p 22 -i ~/.ssh/lightsail.rsa
9-download package list sudo apt-get update
10-upgrade the packages sudo apt-get upgrade
11-change port in server dashboard to 2200
12-change the port from 22 to 2200 
   sudo nano /etc/ssh/sshd_config
13-connect 
  ssh ubuntu@3.8.236.91 -p 2200 -i ~/.ssh/lightsail.rsa
  
14-Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)  using 
    sudo ufw allow 2200/tcp
    sudo ufw allow 80/tcp
    sudo ufw allow 123/udp
    sudo ufw deny 22
    sudo ufw enable
15-Configure the local timezone to UTC
     sudo dpkg-reconfigure tzdata and then choose UTC

16- add user grader using sudo adduser grader password eng2019
17-give access sudo to grader using 
   sudo visudo 
   and add grader ALL=(ALL:ALL)ALL
   
17-Configure key-based authentication for grader user
   Disable ssh login for root user
    Run sudo nano /etc/ssh/sshd_config
    Change PermitRootLogin without-password line to PermitRootLogin no
    Restart ssh with sudo service ssh restart
    Now you are only able to login using ssh -i ~/.ssh/lightsail.rsa -p 2200 grader@3.8.236.91
    

18-install Apache
    sudo apt-get install apache2
19- install mod_wsgi


20-Clone the Catalog app from Github

   Install git using: sudo apt-get install git
   cd /var/www
   sudo mkdir catalog
   change owner of the newly created catalog folder sudo chown -R grader:grader catalog
   cd  catalog
   Clone your project from github git clone https://github.com/sabreensalama/catalogitem.git catalog
   Create a catalog.wsgi file
   rename application.py to __init__.py in path /var/www/catalog/catalog
21-install virtual environment using sudo pip install virtualenv
    create new envirnoment called venv  sudo virtualenv venv
    activate this uisng   source venv/bin/activate
    make permissopn to it using chmod 777 venv
    
  22- install pip using sudo apt install python-pip
      install falsk pip install flask
      sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils
23-update json-file path
24-Configure and enable a new virtual host
   sudo nano /etc/apache2/sites-available/catalog.conf
   
<VirtualHost *:80>
    ServerName 3.8.236.91
    ServerAlias ec2-3.8.236.91.us-west-2.compute.amazonaws.com
    ServerAdmin admin@3.8.236.91
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
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
    
 sudo a2ensite catalog
 
 25-install postgresql
 26-create a new PostgreSQL user named catalog with limited permissions

PostgreSQL creates a Linux user with the name postgres during installation; switch to this user by running sudo su - postgres (for security reasons, it is important to only use the postgres user for accessing the PostgreSQL software)

Connect to psql (the terminal for interacting with PostgreSQL) by running psql

Create the catalog user by running CREATE ROLE catalog WITH LOGIN;

Next, give the catalog user the ability to create databases: ALTER ROLE catalog CREATEDB;

Finally, give the catalog user a password eng 2019 by running \password catalog

Check to make sure the catalog user was created by running \du; 


27-Create a Linux user called catalog and a new PostgreSQL database
    Create a new Linux user called catalog:
        run sudo adduser catalog
    Give the catalog user sudo permissions
        run sudo visudo
      search for a line that looks like this: root ALL=(ALL:ALL) ALL
      add the following line below this one: catalog ALL=(ALL:ALL) ALL
       save and close the visudo file

28-While logged in as catalog, create a database called catalog by running createdb catalog
Run psql and then run \l to see that the new database has been created

29-install git 

what helped me to achieve this project is https://github.com/rrjoson/udacity-linux-server-configuration
