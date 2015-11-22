# Project5

## Add user 'grader'
* create user: `adduser grader`
* Create sudo access file for grader: `sudo nano /etc/sudoers.d/grader`
* Add this line to the file, save and exit: `grader ALL=(ALL) NOPASSWD:ALL`
* `grader` now has sudo rights

## SSH login
* All this will be done while logged in as root user.
* Create folders/files and set up permissions
    * `sudo mkdir /home/grader/.ssh`
    * `sudo chmod 700 /home/grader/.ssh`
* Creating `authorized_keys`
    * `sudo cp ~/.ssh/authorized_keys /home/grader/.ssh/authorized_keys`    
    * `sudo chmod 600 /home/grader/.ssh/authorized_keys`
* Make sure user is owner/group for the .ssh folder and keys file
    * `sudo chown grader:grader /home/grader/.ssh/`
    * `sudo chown grader:grader /home/grader/.ssh/authorized_keys`
  * Edit `sshd_config`
    * `sudo nano /etc/ssh/sshd_config` 
    * Find `PasswordAuthentication` and set it to no:
        * `PasswordAuthentication no`
    * Set port to 2200:
        * `Port 2200`
    * Restart the ssh service:
        `sudo service ssh restart`

## Login as grader
`ssh -i ~/.ssh/udacity_key.rsa grader@52.33.147.216 -p 2200`

## Reconfigure Timezone
* `sudo dpkg-reconfigure tzdata`
* Select `None of the above`, press enter.
* Select `UTC``, press enter.

## Update all installed packages
* `sudo apt-get update`
* `sudo apt-get upgrade`

## Configure firewall
* Set up the defaults: Deny all incoming, and allow all outgoing, and deny port 22 (SSH), since we are setting it to 2200
    * `sudo ufw default deny incoming`
    * `sudo ufw default allow outgoing`
    * `sudo ufw deny 22`
* Set up the minimal ports we need open for this VM to function as server
    * `sudo ufw allow 2200`
    * `sudo ufw allow 80`
    * `sudo ufw allow 123`
* Start the firewall
    * `sudo ufw enable`

## Install git
* `sudo apt-get install git`

## Install Python, Flask and related tools
* `sudo apt-get -y install python-flask python-sqlalchemy`
* `sudo apt-get -y install python-psycopg2`
* `sudo apt-get -y install python-pip`
* `sudo pip install oauth2client`
* `sudo pip install Flask-Restless`
* `sudo pip install marshmallow`
* `sudo pip install Flask-Uploads`

## Install Apache
* Install Apache: `sudo apt-get install apache2`
* Install mod_wsgi: `sudo apt-get install libapache2-mod-wsgi`
* Setting up Apache
    * Edit the website config: `sudo nano /etc/apache2/sites-enabled/000-default.conf`
        * Set Servername: `ServerName [ip address]`
        * Set the document root: `DocumentRoot /var/www/html/ItemCatalog`
        * Add this line before the `</VirtualHost>` closing tag: `WSGIScriptAlias / /var/www/html/ItemCatalog/myapp.wsgi`
* Clone repository: `sudo git clone https://github.com/acivux/ItemCatalog.git /var/www/html/ItemCatalog`
* Create the mod_wsgi `sudo nano /var/www/html/ItemCatalog/myapp.wsgi`:
```
import sys, os
sys.path.append("/usr/local/lib/python2.7/dist-packages/oauth2client")
sys.path.insert (0,'/var/www/html/ItemCatalog')
os.chdir("/var/www/html/ItemCatalog")
from application import app as application
```
* Create testing_user:
    * `sudo cp /var/www/html/ItemCatalog/testing_user_sample.py /var/www/html/ItemCatalog/testing_user.py`
    * `sudo nano /var/www/html/ItemCatalog/testing_user.py`
    * Add your username and email address


## Install PostgreSQL
* `sudo apt-get install postgresql postgresql-contrib`
* Log in as postgres user: `sudo -i -u postgres`
* Create user: `psql -c "CREATE USER catalog WITH createdb LOGIN PASSWORD 'horlosie';"`
* Create database: `createdb -O catalog catalog`
* Remove all other user rights
    * `psql -U postgres -d catalog -c "REVOKE ALL ON SCHEMA public FROM public;"`
    * `psql -U postgres -d catalog -c "GRANT ALL ON SCHEMA public TO catalog;"`

# Sources
https://discussions.udacity.com/t/project-5-resources/28343