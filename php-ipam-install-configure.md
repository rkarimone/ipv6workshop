**How To Install phpIPAM on Ubuntu 20.04**

The objective of this guide is to help you Install and Configure phpIPAM on Ubuntu 20.04/18.04 Linux distribution. phpIPAM is an open-source php-based web IP address management application (IPAM). Its goal is to provide light, modern and useful IP address management. phpIPAM uses MySQL database backend and jQuery libraries, Ajax and HTML5/CSS3 features.

```
phpIPAM has the following features:
    • IPv4/IPv6 IP address management
    • Section / Subnet management
    • Automatic free space display for subnets
    • Visual subnet display
    • Automatic subnet scanning / IP status checks
    • PowerDNS integration
    • NAT support
    • VLAN management
    • VRF management
    • IPv4 / IPv6 calculator
    • IP database search
    • E-mail notifications
    • Custom fields support
    • Translations
    • Changelogs
    • RACK management
    • Domain authentication (AD, LDAP, Radius)
    • Per-group section/subnet permissions
    • Device/device types management
    • RIPE subnets import
    • XLS / CVS subnets import
    • IP request module
    • REST API
    • Locations module

```

**Install phpIPAM on Ubuntu 20.04/18.04**

phpIPAM has a number of dependencies that we need to install before we can install and configure phpIPAM. These are:
    1. MySQL / MariaDB server
    2. php / php-fpm for nginx
    3. php modules
    4. Apache / nginx web server
    5. phpIPAM domain – ipam.example.com (should be replaced with your domain)
    
**Step 1: Install MariaDB Server**

Start with the installation of MariaDB database server:


    sudo apt update
    sudo apt install mariadb-server mariadb-client


Ensure mariadb service is started and set to start at boot:

    sudo systemctl enable mariadb
    sudo systemctl start mariadb

Secure database server by setting root password:
    
    sudo mysql_secure_installation



Once the database installation and setup is complete, create a database for phpipam user:

```
$ sudo mysql -u root -p
CREATE USER 'phpipam'@'127.0.0.1' IDENTIFIED BY 'PASSWORD-HERE';
GRANT ALL PRIVILEGES ON . TO 'phpipam'@'127.0.0.1' WITH GRANT OPTION;
FLUSH PRIVILEGES;
QUIT;

```

**Step 2: Install PHP and required modules**

Next phase is the installation of php and required modules. Run the following commands:
sudo apt update 
sudo apt -y install php php-{mysql,curl,gd,intl,pear,imap,memcache,pspell,tidy,xmlrpc,mbstring,gmp,json,xml,fpm}

**Step 3: Install phpIPAM on Ubuntu 20.04/18.04 Linux**
We’ll download phpIPAM from Github. Install git first:

    sudo apt -y install git

Clone phpIPAM code from github

    sudo git clone --recursive https://github.com/phpipam/phpipam.git /var/www/html/phpipam

Change to clone directory.

    cd /var/www/html/phpipam

You can also download phpipam from official Sourceforge repository and extract it to your web server directory.

**Step 4: Configure phpIPAM on Ubuntu 20.04/18.04**

Change your working directory to /var/www/html/phpipam and copy config.dist.php to config.php, then edit it.
sudo cp config.dist.php config.php

Edit the file to configure database credentials as added on Step 1:

    sudo vim config.php
    /**
    * database connection details
    ******************************/
    $db['host'] = 'localhost';
    $db['user'] = 'phpipam';
    $db['pass'] = 'PASSWORD-HERE';
    $db['name'] = 'phpipam';
    $db['port'] = 3306;

**Option 1: Using Nginx Web server**

Install nginx using the command:

    sudo systemctl stop apache2 && sudo systemctl disable apache2
    sudo apt -y install nginx

Configure nginx:

    sudo vim /etc/nginx/conf.d/phpipam.conf

Add content:
Ubuntu 20.04:

```

server {
    listen       80;
    # root directory
    server_name ipam.example.com  www.ipam.example.com;
    index        index.php;
    root   /var/www/html/phpipam;

location / {
            try_files $uri $uri/ /index.php$is_args$args;
        }

location ~ \.php$ {
            try_files $uri =404;
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
             fastcgi_pass   unix:/run/php/php-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_index index.php;
            include fastcgi_params;
        }

 }
 
 ```
 
Change ownership of the /var/www/ directory to www-data user and group.

    sudo chown -R www-data:www-data /var/www/html
    sudo systemctl restart nginx


**Step 5: Finish phpIPAM Installation on Ubuntu 20.04/18.04**

Start the installation process by visiting http://ipam.example.com, replace ipam.example.com with your valid domain name. The URL could also be http://domain.com/phpipam or IP Address instead of DNS name depending on your configuration.
http://ipam.example.com
If the domain name used is not a valid DNS record you can modify your hosts file:

# Linux / macOS
$ sudo vim /etc/hosts
172.21.200.10 ipam.example.com


On the first page, Select “New phpipam installation“
Since we had created a database, we’ll go with “Automatic database installation“.
For Automatic database installation, set like below.
On successful installation, you should get the admin login page.

**The default Login credentials are:**

    Username: admin
    Password: ipamadmin


You’re prompted to change the admin password on the first login.
You have successfully installed phpIPAM on Ubuntu 20.04 / Ubuntu 18.04 Linux system.



