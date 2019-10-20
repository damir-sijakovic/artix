# Artix Linux - Web Development Server

Archlinux with OpenRC guide 

---

## Intro

We will install Nginx server, PHP (programming language) and later we will add MYSQL database.

## Prepare directory

We will use /srv/webdev directory as root for our web development server.

    sudo mkdir /srv/webdev
    sudo touch /srv/webdev/index.html
    sudo echo "HELLO HTML" > /srv/webdev/index.html 
    sudo touch /srv/webdev/test.php
    sudo echo "<?php echo 'HELLO PHP'; ?>" > /srv/webdev/test.php 
    
## Install packages

Run:

    sudo pacman -S php php-fpm php-fpm-openrc nginx nginx-openrc composer
  

## Setup Nginx

Backup original conf:

    sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf_old

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

            }
          }


## Start services and test in browser

Start services:

      rc-service php-fpm start
      rc-service nginx start

Test html:

      firefox http://localhost:8080
    
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

## Mysql database

Currently best open source SQL server is MariaDB.
To install it run:

    sudo pacman -S mysql

Before you start service add user and data directory:

    sudo mysql_install_db --user=mysql --datadir=/var/lib/mysql

Start service:
    
    sudo rc-service mysql start
    
Note: If you get error 'Could not open required defaults file: /etc/mysql/my.cnf'
use AlpineLinux init script for MariaDB:
    
    wget https://git.alpinelinux.org/aports/plain/main/mariadb/mariadb.initd
    sudo cp mariadb.initd /etc/init.d/mariadb
    sudo chmod 755 /etc/init.d/mariadb
    sudo mysql_install_db --user=mysql --datadir=/var/lib/mysql
    sudo rc-service mariadb start
        
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
    
In database:

    show databases;
    exit;
    
## phpMyAdmin

Install phpmyadmin:

    sudo pacman -S phpmyadmin 

After installing, edit php.ini file,

    sudo nano /etc/php/php.ini

Make sure the following lines are uncommented:

    extension=bz2.so
    extension=mysqli.so

Link webapps directory:

    sudo ln -s /usr/share/webapps/ /srv/webdev/webapps

Check in browser:

    firefox http://localhost:8080/webapps/phpMyAdmin/index.php
    
Enter mysql username/password you have set with mysql_secure_installation.

## Install IDE

The PHP IDE project delivers a PHP Integrated Development Environment framework for the Eclipse platform.
To install run:

    sudo pacman -S eclipse-php


---
