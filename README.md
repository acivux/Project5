# Project5

## Add user 'grader'
* create user: `adduser grader`
* Create sudo access file for grader: `sudo nano /etc/sudoers.d/grader`
* Add this line to the file, save and exit: `grader ALL=(ALL) NOPASSWD:ALL`
* `grader` now has sudo rights

## SSH login
* Creating `~/.ssh/authorized_keys` will be left up to the user since adding the key is somewhat different on Windows and Linux. Please read the [Archlinux wiki](https://wiki.archlinux.org/index.php/SSH_keys) for more information. I am using Windows, and a simple copy/paste worked for me.
* Create folders/files and set up permissions
    * `sudo mkdir ~/.ssh`
    * `sudo chmod 700 ~/.ssh`
    * `sudo chmod 600 ~/.ssh/authorized_keys`
* Make sure user is owner/group for the .ssh folder and keys file
    * `sudo chown grader:grader .ssh/`
    * `sudo chown grader:grader .ssh/authorized_keys``
* Edit `sshd_config` to disable password login
    * `sudo nano /etc/ssh/sshd_config` 
    * Find `PasswordAuthentication` and set it to no:
        * `PasswordAuthentication no`
    * Restart the ssh service:
        `sudo service ssh restart`

## Login as grader
`ssh -i ~/.ssh/udacity_key.rsa grader@52.32.72.130 -p 2200`

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


## Reconfigure Timezone
* `sudo dpkg-reconfigure tzdata`
* Select `None of the above`, press enter.
* Select `UTC``, press enter.

## Update all installed packages
* `sudo apt-get update`
* `sudo apt-get upgrade`

## Install Apache
* Install Apache: `sudo apt-get install apache2`
* Install mod_wsgi: `sudo apt-get install libapache2-mod-wsgi`
* Setting up Apache
    * Edit `/etc/apache2/sites-enabled/000-default.conf`
        * Set the document root: `DocumentRoot /var/www/html/ItemCatalog`
        * Add this line before the `</VirtualHost>` closing tag: `WSGIScriptAlias / /var/www/html/ItemCatalog/myapp.wsgi`
* Create the mod_wsgi file
```
import sys, os
sys.path.append("/usr/local/lib/python2.7/dist-packages/oauth2client")
sys.path.insert (0,'/var/www/html/ItemCatalog')
os.chdir("/var/www/html/ItemCatalog")
from application import app as application
```

## Install PostgreSQL
*

 
## Clone ItemCatalog
* install git #ToDo: Add command
* sudo git clone https://github.com/acivux/ItemCatalog.git


# Sources
https://discussions.udacity.com/t/project-5-resources/28343