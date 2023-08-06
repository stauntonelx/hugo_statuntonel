---
title: "Arch Nginx en wordpress"
date: "2021-01-25"
draft: false
author:
description: Arch webserver met wordpress en nginx
categories:
  - Server
tags:
  - arch
  - webserver
  - wordpress
slug: arch-webserver-wordpress
toc: true
---

Hoe installeer wordpress op een arch lemp server.

<!--more-->

## 01 nginx
installeer nginx

    pacman -S nginx

start en test of nginx werkt:

    systemctl enable nginx
    systemctl start nginx

check nu of de testpagina tevoorschijn komt door in een browser de url op te vragen.

stop de service en configureer nginx:

    systemctl stop nginx

stel orginele config veilig:

    cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf_org

Daarna vervang je de inhoud als volgt:

    user http http;

    worker_processes auto;

    events {
    worker_connections 1024;
    # multi_accept on;
    }

    http
    {
    # rewrite_log on;
    include mime.types;
    default_type application/octet-stream;
    access_log /var/log/nginx/access.log;
    sendfile on;
    # tcp_nopush on;
    keepalive_timeout3;
    # tcp_nodelayon;
    # gzip on;
    #php max upload limit cannot be larger than this
    client_max_body_size 13m;
    indexindex.php index.html index.htm;

    # Upstream to abstract backend connection(s) for php
    upstream php
    {
    server unix:/var/run/php-fpm/php-fpm.sock;
    }

    server {
    server_name domain.tld;
    root /usr/share/nginx/html/wp;

    location = /favicon.ico {
    log_not_found off;
    access_log off;
    }

    location = /robots.txt {
    allow all;
    log_not_found off;
    access_log off;
    }

    location / {
    # This is cool because no php is touched for static content.
    # include the "?$args" part so non-default permalinks doesn't break when using query string
    try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
    #NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
    include fastcgi.conf;
    fastcgi_intercept_errors on;
    fastcgi_pass php;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
    expires max;
    log_not_found off;
    }
    }
    }

## 02 wordpress

maak en nieuwe wordpress directory:

    mkdir -p /usr/share/nginx/html/wp

geef de juiste rechten aan de wordpress map:

    cd /usr/share/nginx/html/
    chgrp http wp
    chmod 3770 wp

download wordpress:

    cd /root
    wget https://nl.wordpress.org/wordpress-4.3.1-nl_NL.tar.gz
    tar -xvf wordpress-4.3.1-nl_NL.tar.gz
    cd wordpress
    rsync -rv ./ /usr/share/nginx/html/wp

ga naar de wp map:

    cd /usr/share/nginx/html/wp
    chmod -R g+w .

## 03 mariadb
installatie

    pacman -S mariadb php-mysql
    mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql

start service

    systemctl enable mysqld
    systemctl start mysqld

beveilig mariadb (eerste vraag met enter beantwoorden)

    mysql_secure_installation

vragen spreken voor zich

voor Wordpress moet nu een database en gebruiker worden opgenomen

    mysql -u root -p

op de sqlprompt geef je de volgende commandos in:

    create database wp;
    create user wp@localhost identified by 'Sheela123';
    grant all privileges on wp.* to wp@localhost;
    flush privileges;
    quit

## 04 php
nginx maakt gebruik van PHP-FPM

    pacman php php-fpm
    nano /etc/php/php.ini

verander deze regel

    open_basedir = /usr/share/nginx/html/wp:/usr/share/webapps/

en

    cgi.fix_pathinfo = 0

voor mariadb deze regels activeren:

    extension=mysqli.so
    extension=pdo_mysql.so

enable service

    systemctl enable php-fpm

[Meer server](/categories/server)
