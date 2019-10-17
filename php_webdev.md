# Artix Linux - Web Development Server

Archlinux with OpenRC guide 

---

## Intro

We will install Nginx server, PHP (programming language) and later we will add MYSQL database.

## Prepare directory

We will use /srv/webdev directory as root for our web development server.

    sudo mkdir /srv/webdev
    sudo touch /srv/webdev/index.html
    sudo echo /srv/webdev/index.html > "HELLO HTML"
    sudo touch /srv/webdev/test.php
    sudo echo /srv/webdev/test.php > "<?php echo 'hello php!'; ?>"
    
## Install packages

Run:

    sudo pacman -S php php-fpm php-fpm-openrc nginx nginx-openrc    
  

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

      rc-service php-fpm-openrc start
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
    echo "<?php echo 'hello user-writable php!'; ?>" /srv/webdev/my_projects/test.php
    firefox http://localhost:8080/my_projects/test.php


---
