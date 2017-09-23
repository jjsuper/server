# Linux Server Configuration
This is the fifth project for "Full Stack Web Developer Nanodegree" on Udacity. 

In this project, take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

You can visit http://54.245.155.141 for the website deployed.

## Tasks
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. 
2. Follow the instructions provided to SSH into your server.
3. Update all currently installed packages.
4. Change the SSH port from 22 to 2200.
5. Configure the Uncomplicated Firewall (UFW).
6. Create a new user account named grader.
7. Give grader the permission to sudo.
8. Create an SSH key pair for grader using the ssh-keygen tool.
9. Configure the local timezone to UTC.
10. Install and configure Apache to serve a Python mod_wsgi application.
11. Install and configure PostgreSQL.
12. Install git.
13. Clone and setup your Item Catalog project.
14. Set it up in your server.

### Instruction for SSH access to the server
1. Download private key from "Notes to Reviewer".
2. Move the private key file into `~/.ssh/Udacity`.
3. Change the file permission. In your termial, type in 
	```
	chmod 600 ~/.ssh/Udacity
	```
4. Access to the server. In your termial, type in
	```
	ssh grader@54.245.155.141 -p 2200 -i ~/.ssh/Udacity
	```

### Create a New User
1. In server termial, type in
	```
	sudo adduser grader
	```
2. Give `sudo` access
	```
	sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
	```
3. Then modify `/etc/sudoers.d/grader` to `grader ALL=(ALL) NOPASSWD:ALL`.

### Install a Public Key
1. In client, use `ssh-keygen` to generate two files. 
	Private Key `~/.ssh/Udacity`
	Public  Key `~/.ssh/Udacity.pub`
2. In server termial, type in 
	```
	mkdir .ssh
	touch .ssh/authorized_keys
	nano .ssh/authorized_keys
	```
3. Copy `~/.ssh/Udacity.pub` to `.ssh/authorized_keys`.
4. Change permission
	```
	chmod 700 .ssh
	chmod 644 .ssh/authorized_keys
	```
### Update all currently installed packages
In the terminal, type in

	```
	sudo apt-get update
	sudo apt-get upgrade
	```

### Change SSH port from 22 to 2200.
1. Modify `sshd_config` file to `Port 2200` and `PasswordAuthentication no`.
	```
	sudo nano /etc/ssh/sshd_config
	```
2. Restart service ssh
	```
	sudo service ssh restart
	```
3. Make sure to configure the Lightsail firewall to allow it.

### Configure Uncomplicated Firewall(UFW)
1. To update Lightsail firewall, add a new rule in Networking setting.

Application | Protocol  | Port range	
----------- | --------- | ----------
HTTP        | TCP       | 80	
Custom      | TCP       | 2200

2. In the terminal, type in
	```
	sudo ufw status
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	sudo ufw allow 2200
	sudo ufw allow 80
	sudo ufw allow 123
	sudo ufw enable
	```


### Configure the local timezone to UTC
Configure the time zone by 
	```sudo dpkg-reconfigure tzdata```

### Install and configure Apache
1. Install Apache by `sudo apt-get install apache2`.
2. Install mod_wsgi by `sudo apt-get install libapache2-mod-wsgi`.
3. Restart Apache by `sudo apache2ctl restart`.

### Install and configure PostgreSQL
1. Install PostgreSQL by `sudo apt-get install postgresql`.
2. Check if no remote connections are allowed in `sudo vim /etc/postgresql/9.5/main/pg_hba.conf`.
3. Login as user "postgres" by `sudo su - postgres`.
4. Login PostgreSQL by `psql`.
5. Create a new database named "catalog" by `postgres=# CREATE DATABASE catalog;`.
6. Create a new user named "catalog" by `postgres=# CREATE USER catalog;`.
7. Create a password for user "catalog" by `postgres=# ALTER ROLE catalog WITH PASSWORD 'password';`.
8. Give user "catalog" permission to database "catalog" by 	`postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`.
9. Quit PostgreSQL by `postgres=# \q`.
10. Exit from user "postgres" by `exit`.

### Clone and setup Item Catalog project
1. Move to home directory by `cd`.
2. Clone Item Catalog project by `git clone https://github.com/jjsuper/catalog.git`.
3. Move to /var/www directory by `cd /var/www`.
4. Create a new directory by `sudo mkdir catalog`.
5. Copy catalog project files into the new directory by `sudo mv ~/catalog ./catalog`.
6. Rename `application.py` to `__init__.py`.
7. Edit `*.py` and change `engine = create_engine('sqlite:///categoryitem.db')` to `engine = create_engine('postgresql://catalog:password@localhost/catalog')`.
8. Edit `__init__.py` and add `path = os.path.dirname(__file__)`. Replace all `'client_secrets.json'` by `path+'client_secrets.json'`.


### Install python packages
1. Install pip by `sudo apt-get install python-pip`.
2. Install following prerequisite packages.
	```
	sudo pip install sqlalchemy 
	sudo pip install Flask
	sudo pip install oauth2client
	sudo pip install requests
	sudo pip install psycopg2
	```

### Modify client_secrets.json
1. Add `http://54.245.155.141` in authorized javaScript origins in Google Web Client.
2. Download JSON file and replace `client_secrets.json`.
2. Add `http://54.245.155.141` in valid OAuth redirect URIs in Facebook Web Client.

### Configure Server
1. Edit the file `/etc/apache2/sites-enabled/000-default.conf`.
	```
	<VirtualHost *:80>
        ServerAdmin wlzhujiang@gmail.com
        WSGIScriptAlias / /var/www/catalog/myapp.wsgi
        Alias /static /var/www/catalog/catalog/static
        <Directory /var/www/catalog/catalog/>
                Order allow,deny
                Allow from all
        </Directory>
        <Directory /var/www/catalog/catalog/static/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>
	```
2. Create the myapp.wsgi File.
	```
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/catalog/")

	from FlaskApp import app as application
	application.secret_key = 'super secret key'
	```
3. Restart Apache `sudo service apache2 restart`.





