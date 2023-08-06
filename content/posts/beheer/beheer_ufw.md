---
title: Uncomplicated Firewall
date: 2021-02-08T23:07:47+01:00
draft: false
author:
description: "UFW: basic regels voor ssh ping uitzetten en fail2ban"
categories:
  - beheer
tags:
  - Debian
  - beveiliging
slug: beheer-debian-beveiliging-ufw
---

Beveilig je server met UFW, fail2ban installatie en het uitzetten ping.

<!--more-->

## 01 Uncomplecated Firewall (UFW)

Eerst zorgen we voor een extra protectie voor botnet attacks:

    apt-get install fail2ban

Daarna installeren we de firewall:

    apt-get install ufw

Check de status:

    ufw status

Als het goed is zegt de firewall dat de status inactive is.
Voor we hem aanzetten moeten we toestemming geven voor SSH op poort 22

    ufw default deny incoming
    ufw default allow outgoing
    ufw allow 22/tcp
    ufw allow http

Nu kunnen we de firewall inschakelen zonder dat de ssh-sessie word beeindigd.

    sudo ufw enable

Check nu de status:

    ufw status numbered

Je krijgt dan een dergelijke output:

          To                         Action      From
         --                         ------      ----
    [ 1] 22/tcp                     ALLOW IN    Anywhere
    [ 2] 22/tcp (v6)                ALLOW IN    Anywhere (v6)

Je kunt ook een app profile gebruiken, lijst daarvoor eerst de aanwezige app's uit.

    ufw app list
    ufw allow "Nginx-Full"

## 02 disable ping

Poortscanning gebeurd vaak door een ip / poort te pingen. Op de volgende wijze
kun je pingen uitzetten:

    nano /etc/ufw/before.rules

Voor alle regels met "--icmp" verander ACCEPT in DROP.
Daarna kun je de firewall opnieuw starten door:

    ufw reload

[Meer beheer](/categories/beheer)
