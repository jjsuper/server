# Linux Server Configuration
This is the fifth project for "Full Stack Web Developer Nanodegree" on Udacity. 

In this project, take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

You can visit http://54.245.155.141 for the website deployed.

## Summary of Installed Software
```
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
sudo apt-get install postgresql
```



## Tasks
1. Generate key 
2. 
3. Update all currently installed packages.
4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

Warning: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. Review this video for details! When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.
Give grader access.
In order for your project to be reviewed, the grader needs to be able to log in to your server.

6. Create a new user account named grader.
7. Give grader the permission to sudo.
8. Create an SSH key pair for grader using the ssh-keygen tool.

Prepare to deploy your project.
9. Configure the local timezone to UTC.
10. Install and configure Apache to serve a Python mod_wsgi application.

If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3 11. Install and configure PostgreSQL:
Do not allow remote connections
Create a new database user named catalog that has limited permissions to your catalog application database.
12. Install git.

Deploy the Item Catalog project.
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!



### Instruction for SSH access to the server
1. Download private key from "Notes to Reviewer".
2. Move the private key file into `~/.ssh/Udacity`.
3. Change the file permission. In your termial, type in 
	```chmod 600 ~/.ssh/Udacity```
4. Access to the server. In your termial, type in
	```ssh grader@54.245.155.141 -p 2200 -i ~/.ssh/Udacity

### Create a New User
1. In server termial, type in
	```sudo adduser grader```
2. Give `sudo` access
	```sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader```
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
3. Copy `~/.ssh/Udacity.pub` to `.ssh/authorized_keys`
4. Change permission.
	```
	chmod 700 .ssh
	chmod 644 .ssh/authorized_keys
	```

4. Secure Server
Update all currently installed packages
```
sudo apt-get update
sudo apt-get upgrade
```
Change SSH port from 22 to 2200.
```
sudo nano /etc/ssh/sshd_config
```
Modify corresponding lines in `sshd_config` to

`
Port 2200

PasswordAuthentication no
`
```
sudo service ssh restart
```

5. Configure Uncomplicated Firewall(UFW)
To update Lightsail firewall, add a new rule in Networking setting.

Application | Protocol  | Port range	
----------- | --------- | ----------
HTTP        | TCP       | 80	
Custom      | TCP       | 2200

UFW
```
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200
sudo ufw allow 80
sudo ufw allow 123
sudo ufw enable
```

### Web Application Configuration

1. Configure Apache

Configure Apache to handle requests using the WSGI module. You’ll do this by editing the `/etc/apache2/sites-enabled/000-default.conf` file.

Add the following line at the end of the <VirtualHost *:80> block, right before the closing </VirtualHost> line: 

`WSGIScriptAlias / /var/www/html/myapp.wsgi`

Finally, restart Apache with the `sudo apache2ctl restart` command.


2. 