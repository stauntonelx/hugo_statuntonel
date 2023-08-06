---
title: Ubuntu lemp
date: 2021-01-25T21:15:09+01:00
author:
draft: false
description: nginx,mariadb, php installeren onder ubuntu
categories:
  - server
tags:
  - ubuntu
  - webserver
slug: ubuntu-webserver
toc: true
---

Hieronder mijn aantekeningen over het installeren van een lemp onder Ubuntu. Op de server voeren we er eerst een basis installatie uit. Daarna installeren we de lemp software.

<!--more-->

## 01 mariadb

    apt-get install mariadb-server
    mysql_secure_installation

Beantwoord alle vragen van het script met Yes en geef root een wachtwoord.
Log daarna in met je root wachtwoord:

    mysql -u root -p

Check even de datatabases

    show databases;
    quit;

check onder welke gebruiker de service draait

    ps -aux| grep mysql

Als het goed is, is dat de gebruiker mysql.
Verder kun je de status checken met:

    systemctl status mysql

## 02 nginx

    apt install nginx curl

Als de service goed draait krijg je nu een testpagina op het ip/adres van de server.

    curl ipadres
    systemctl status nginx

Voordat je nginx instellingen gaat aanpassen maken we een kopie

    cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf_org

Met het tree commando krijg je een mooi overzicht van de sites-available, sites-enabled

    apt-get install tree
    cd /etc/nginx
    tree

check onder welke gebruiker nginx draait:

    ps aux| grep nginx

## 03 php

zie ook dit [arch wordpress]({{< ref "/posts/server/server_ubuntu_lemp" >}} "wordprees onder arch")

[Meer server](/categories/server)
