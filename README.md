# Item Catalog App Project



# 
Your README.md file should include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.


Locate the SSH key you created for the grader user.
During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.


## IP address
54.245.155.141

## SSH port
2200

## URL

## Summary of installed software

sudo apt-get install apache2



## Summary of configuration




1. Generate Key Pairs
In Client PC,
```
ssh-keygen
~/.ssh/Udacity
~/.ssh/Udacity.pub
```

2. Create a New User
```
sudo adduser grader
```
Give `sudo` access
```
sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
```
Then modify `/etc/sudoers.d/grader` to
`grader ALL=(ALL) NOPASSWD:ALL`

3. Install a Public Key
```
mkdir .ssh
touch .ssh/authorized_keys
nano .ssh/authorized_keys
```
Copy `~/.ssh/Udacity.pub` to `.ssh/authorized_keys`
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
Application\tProtocol\tPort range	
HTTP\tTCP\t80	
Custom\tTCP\t2200

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
