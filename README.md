### Linux Server Configuration
Udacity Full Stack Nanodegree Fianl Project

### Server details
IP address: ```52.194.250.57```
ssh port: ```2200```

### Configuration changes
# Configuration of Uncomplicated Firewall(UFW)
```
ufw allow 80/tcp
ufw allow 2200/tcp
ufw allow 123/tcp
ufw enable
```
To check the status of firewall, use:
```
ufw status
```

# Change SSH port from 22 to 2200
Edit the file ```/etc/ssh/sshd_config``` and change the line ```Port 22``` to:
```Port 2200```
Restart ssh service
```service ssh restart```

# Update all currently installed packages
```
apt-get update
apt-get upgrade
```

# Add user grader
```
adduser grader
```

# Give grader sudo access
Go to ```/etc/sudoers.d/grader``` and add the following line:
```
grader ALL=(ALL:ALL) NOPASSWD=ALL
```

# Set up ssh key for grader
On your local machine, go to ```~/.ssh``` and run the following command:
```ssh-keygen -t rsa```
and copy the public key to server:
```ssh-key-copy -i YOUR_PUBLIC_KEY grader@52.194.250.57```
Can now login as grader using the command: ```ssh -i ~/.ssh/PRIVATE_KEY grader@52.194.250.57 -p 2200```

# Change timezone to UTC
```
timedatectl set-timezone UTC
```

# Install package to serve web applications
``` 
apt-get install apache2
apt-get install libapache2-mod-wsgi
```

# Install and configure PostgreSQL
Install PostgreSQL with:
```
apt-get install postgresql postgresql-contrib
```
Conenct to PostgreSQL:
```
sudo -u postgres psql
```
Set a password for postgres:
```
AFTER USER postgres PASSWORD 'password'
```
Go to ```/etc/postgresql/9.5cd/main/pg_hba.conf``` and change authentication method from ```peer``` to ```md5```

# Install Git
```
apt-get install git
```

# Clone the repository and set up environment
```
cd /var/www/
git clone https://github.com/StillFantastic/Catalog.git Catalog
```
and follow the instruction in repository to set up environment

# WSGI file
inside your repository, create a new file project.wsgi:
```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/Catalog/")

from bookCatalog import app as application
```

# Configure the setting of apache
Edit the file ```/etc/apache2/sites-available/000-default.conf```
```
<VirtualHost *:80>
  ServerName XX.XX.XX.XX
  ServerAdmin xxxxxxx@gmail.com
  WSGIScriptAlias / /var/www/Catalog/project.wsgi
  <Directory /var/www/Catalog/>
      Order allow,deny
      Allow from all
  </Directory>
  Alias /static /var/www/Catalog/static
  <Directory /var/www/Catalog/static/>
      Order allow,deny
      Allow from all
  </Directory>
</VirtualHost>
```

# Restart Apache to launch the app
```
service apache2 restart
```
