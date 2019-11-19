# Artix Linux - Web Development Server

Archlinux with OpenRC guide 

---

## Intro

We will install Nginx server, PHP (programming language) and later we will add MYSQL database.
Server is at port 8080 (replace it with another: 8888, 8000, 8008... here in text).

## Prepare directory

We will use /srv/webdev directory as root for our web development server.

    sudo mkdir /srv/webdev
    sudo nano /srv/webdev/index.html
        add: HELLO HTML 
    sudo nano /srv/webdev/test.php
        add: <?php echo 'HELLO PHP'; ?>
    
## Install packages

Run:

    sudo pacman -S php php-fpm php-fpm-openrc nginx nginx-openrc composer php-intl unzip
  

## Setup Nginx

Backup original conf:

    sudo mv /etc/nginx/nginx.conf /etc/nginx/nginx.conf_old

Make new conf file:

    sudo nano /etc/nginx/nginx.conf
    
And add new configuration:

          # user webdev webdev;

          worker_processes  1;

          events {
              worker_connections  1024;
          }


          http {	
            include       mime.types;
            default_type  application/octet-stream;
            sendfile        on;
            keepalive_timeout  65;

            server {
              listen       8080;
              server_name  localhost;


              location / {
                allow 127.0.0.1;
                deny all;				
                autoindex on;
                root    /srv/webdev;
                index index.html index.htm index.php;

              }

              error_page   500 502 503 504  /50x.html;

              location = /50x.html {
                root    /srv/webdev;
              }

              location ~ \.php$ {	 
                allow 127.0.0.1;
                deny all;
                root    /srv/webdev;	  
                fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;
                fastcgi_index  index.php;
                include        fastcgi.conf;
              }
              
               location ~ ^/(status|ping)$ {
                allow 127.0.0.1;        
                fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;
                fastcgi_index  index.php;
                include        fastcgi.conf;
               }


            }
          }


## php-fpm monitor

Monitor FastCGI connection between PHP and NGINX. 

In file: 
    
    /etc/php/php-fpm.d/www.conf 

uncomment:

    pm.status_path = /status 

php-fpm status page:
![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/phpfpm.png)

## Start services and test in browser

Start services:

      rc-service php-fpm start
      rc-service nginx start

Test html:

      firefox http://localhost:8080

Test php-fpm status page:

      http://localhost:8080/status?html&full
    
Test php:

      firefox http://localhost:8080/test.php

## Service restart/stop
    
To stop or restart services use:

        rc-service nginx restart
        rc-service nginx stop
    
## Add user writable directory

Here we will add dir '/srv/webdev/my_projects' and
enable file read-write for regular user.

    sudo mkdir /srv/webdev/my_projects
    sudo chown $USER /srv/webdev/my_projects
    echo "<?php echo 'hello user-writable php'; ?>" > /srv/webdev/my_projects/test.php
    firefox http://localhost:8080/my_projects/test.php

## SQL database

Currently best open source SQL server is MariaDB.
To install it run:

    sudo pacman -S mysql

Before you start service add user and data directory:

    sudo mysql_install_db --user=mysql --datadir=/var/lib/mysql

Start service:
    
    sudo rc-service mysql start
    
Note: If you get error 'Could not open required defaults file: /etc/mysql/my.cnf'
use AlpineLinux init script for MariaDB:
    
    sudo cp /etc/init.d/mysql /etc/init.d/mysql_OLD     
    wget https://git.alpinelinux.org/aports/plain/main/mariadb/mariadb.initd    
    sudo cp mariadb.initd /etc/init.d/mariadb
    sudo chmod 755 /etc/init.d/mariadb
    sudo mysql_install_db --user=mysql --datadir=/var/lib/mysql
    sudo rc-service mariadb start

<sub><sup>NOTE: Later in text, use: 'sudo rc-service mariadb ...' instead of 'sudo rc-service mysql ...'.</sup></sub>     
        
Run mysql_secure_installation command:
    
    sudo mysql_secure_installation
    
Follow steps like this:

    * Press enter (You have no root pass by default)
    * Press n to not switch to unix_socket auth (we will use password)
    * Press Y and set/type root password (twice)
    * Press Y to remove anonymous users
    * Press Y to disable root login remotely
    * Press Y to remove test database
    * Press Y to reload privilege table
    
Test DB in terminal (first your Linux root pass and after sql root pass):
 
    sudo mysql -u root -p
    
Test in database:

    show databases;
    exit;
    
To set that only localhost can access database, in file '/etc/my.cnf.d/server.cnf' find 
line #bind-address, uncomment and set to 'bind-address=127.0.0.1'.

    sudo nano /etc/my.cnf.d/server.cnf
        bind-address=127.0.0.1

Restart service

    sudo rc-service mysql restart

## mySQL Audit Log
First check if there is audit plugin.
Run to get plugin path:

    SHOW GLOBAL VARIABLES LIKE 'plugin_dir'

    => /usr/lib64/mysql/plugin/

Look for 'server_audit.so' file.

Add in '/etc/my.cnf.d/server.cnf' under [mysqld].

    # load plugin
    plugin-load=server_audit=server_audit.so
    # do not allow users to uninstall plugin
    server_audit=FORCE_PLUS_PERMANENT
    # only audit connections and DDL queries
    server_audit_events=CONNECT,QUERY
    # enable logging
    server_audit_logging=ON
    # any users who don’t need auditing (csv)
    server_audit_excl_users=’root’
    # flat file
    server_audit_output_type=FILE
    server_audit_file_path=/var/log/mysql_audit.log
    server_audit_file_rotate_size=1000000
    server_audit_file_rotations=9

Check if plugin is loaded in sql database.

    SHOW PLUGINS;

Set log file:

    sudo touch /var/log/mysql_audit.log
    sudo chown mysql /var/log/mysql_audit.log

Restart services, and test with:

    tail -f /var/log/mysql_audit.log


## PHP Error Log

In php.ini set:
    
    error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
    display_errors = Off    
    display_startup_errors = On
    log_errors = On
    error_log = /var/log/php_errors.log

Create log file:

    touch /var/log/php_errors.log
    sudo chown http /var/log/php_errors.log
   
Create somewhere php_error_log.sh with:
    
    #!/bin/bash
    tail -f /var/log/php_errors.log

Or just run in terminal (for example in your php editors terminal):
    
    tail -f /var/log/php_errors.log

![alt text](https://github.com/damir-sijakovic/artix/blob/master/files/geany_php.png)

## phpMyAdmin

Install phpmyadmin:

    sudo pacman -S phpmyadmin 

After installing, edit php.ini file,

    sudo nano /etc/php/php.ini

Make sure the following lines are uncommented:

    extension=bz2.so
    extension=mysqli.so
    extension=intl.so

Restart php-fpm service

    sudo rc-service php-fpm restart

Link webapps directory:

    sudo ln -s /usr/share/webapps/ /srv/webdev/webapps

Check in browser:

    firefox http://localhost:8080/webapps/phpMyAdmin/index.php
    
Enter mysql username/password you have set with mysql_secure_installation.

## Composer (PHP Package Manager)

Composer is an application-level package manager for the PHP programming language 
that provides a standard format for managing dependencies of PHP software and required libraries. 

We will use r/w directory 'my_projects':

    cd /srv/webdev/my_projects/

Install Composer:

    sudo pacman -S composer unzip
    
Install Laravel package:

    composer create-project laravel/laravel ./myapp

Fix r/w directory access

    chmod 777 /srv/webdev/my_projects/myapp/storage/framework/views
    chmod 777 /srv/webdev/my_projects/myapp/storage/framework/sessions
    
Test front page (Add any string at page top)

    nano /srv/webdev/my_projects/myapp/resources/views/welcome.blade.php
    firefox http://localhost:8080/my_projects/myapp/public/


## Install IDE
To install PHP IDE run:

    sudo pacman -S eclipse-php

or

    sudo pacman -S netbeans


---
