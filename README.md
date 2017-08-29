**Linux server configuration**
----------


**About:**

This project created for the Udacity 'Full Stack Developer' Nanodegree.

**Infomation:**

 - IP: `207.154.216.68`, 
 - SSH port: `2200`,
 - The complete URL: `http://207.154.216.68/`

**How I build it:**
*From the DigitalOcean console:*

 - First, I updated all currently installed packages:
	 1. Download package lists with  `apt-get update`
	 2. Fetch new versions of packages with `apt-get upgrade`

 - Installed `finger` by `apt-get install finger` to find information about users

 - I made new user from the DigitalOcean console:   `adduser grader`
 

 - Sudo access for grader:
 - Created a new file in the sudoers directory with `touch /etc/sudoers.d/grader`
 - Edited this file with `grader ALL=(ALL) NOPASSWD:ALL`
 - In my local machine I generated SSH key pairs: `ssh-keygen`

*From bash by user-grader:*
 
 - Connected with `ssh grader@207.154.216.68`
 - Made directory to store ssh-key: `mkdir .ssh`
 - `touch .ssh/authorized_keys`
 - Copy key from my local machine in `linux-project.pub`
 - Paste it in `nano .ssh/authorized_keys`
 - Then `exit` from server
 
*Connected with `ssh grader@207.154.216.68 ~/linux-project`:*

 - Allow ssh connections only by key based authentication `sudo nano /etc/ssh/sshd_config`:
 - `PasswordAuthentication no`
 - Changed SSH port from 22 to 2200:
 - `sudo nano /etc/ssh/sshd_config`
 - Change the port from 22 to 2200
 - Then `exit` from server
 - Restart ssh `sudo service ssh restart`
 
*To sure that everything is work:* 
Connected with `ssh grader@207.154.216.68 -p 2200 ~/linux-project`:

 **Firewall:**
 - `sudo ufw allow 2200`
 - `sudo ufw allow 80`
 - `sudo ufw allow 123`
 - `sudo ufw enable`

**Install Apache:**
    1. `sudo apt-get install apache2`

**Install mod_wsgi:**
 - `sudo apt-get install libapache2-mod-wsgi`

**Restart Apache:**
    1. `sudo apache2ctl restart`

**Install Postgresql and configure it:**

 - `sudo apt-get install postgresql postgresql-contrib`
 - `sudo nano /etc/postgresql/9.1/main/pg_hba.conf`
 Check there are not allowed remote connections
 - Login `sudo su - postgres`
 - Get into postgresql shell `psql`
 - Create a new database  and  a new user:
 `CREATE DATABASE catalog;`
 `CREATE USER catalog;`
 - Password for user *catalog*:
  `ALTER ROLE catalog WITH PASSWORD 'password';`
 - Giving permissions for user *catalog*:
 `GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`
 - Then exit by `\q` and `exit`

**Configure the timezone to UTC:**
 -  `sudo dpkg-reconfigure tzdata`
 -  It is already UTC

**Install git:**
 - `sudo apt-get install git`

**Clone project "Item-catalog":**

 - `cd /var/www`
 - `sudo mkdir App`
 - `cd App`
 - `sudo git clone https://github.com/mikhailsergeevi4/item-catalog.git`
 - `sudo mv ./item-catalog ./App`
 - `cd App`
 - `sudo mv app.py __init__.py`
 - `sudo nano` to edit files: `baseup.py`, `addingcakes.py`, `__init__.py`:
 - `engine=create_engine('sqlite:///cakeswithusers.db') to engine =create_engine('postgresql://
	 catalog:password@localhost/catalog')`
 - change `path` to `client_secrets.json`
 - Make file `requirements.txt` to install all necessary dependencies:
 - `sudo touch requirements.txt`
 - `sudo nano requirements.txt`
 - Install pip: `sudo apt-get install python-pip`
 - `sudo pip install -r requirements.txt`
 - `sudo apt-get -qqy install postgresql python-psycopg2`
 - start database: 
 - `sudo python baseup.py`, 
 -  `sudo python addingcakes.py`
  
**Configure new virtual host:**
 - `sudo nano /etc/apache2/sites-available/App.conf`

    `<VirtualHost *:80>
	ServerName 207.154.216.68
	ServerAdmin mikhailsergeevi4@yandex.ru
	WSGIScriptAlias / /var/www/App/app.wsgi
	<Directory /var/www/App/App/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/App/App/static
	<Directory /var/www/App/App/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>`

 - `sudo a2ensite App`

**WSGI file:**
 - `cd /var/www/App`
 - `sudo nano app.wsgi`

   `#!/usr/bin/python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.append("/var/www/App/")

    from App import app as application
    application.secret_key = 'super_secret_key'

 - `sudo service apache2 reload`


**Thanks:**

 - *DigitalOcean* for useful documentation about how to start server and App on it.
 - *Stackoverflow* for answer the questions.
 - *Udacity* for helpful courses

