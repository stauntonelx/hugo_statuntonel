---
title: Initiele server inrichting
date: 2022-05-15
draft: true
description: Initiele inrichting van een nieuwe server
categories:
  - server
tags:
  - Ubuntu
  - webserver
slug: ubuntu-webserver-initieel
toc: true
---

Na het opnieuw installeren van de server kun je de volgende acties ondernemen.
Daarbij aandacht voor beveiliging (firewall, ssh etc.), opvoeren gebruikers en nog veel meer.
Onderstaande aantekeningen zijn getest met Ubuntu lts 2020.4.

<!--more-->

## 01 toegang

Log in op de server als de root gebruiker.

### software

Installeer alleen de hoognodige software voor de eerste beveiliging.

    apt update
    apt upgrade
    apt install nano tree bash-completion ufw fail2ban

### umask

Verander de umask van 022 naar 027 zoals hier beschreven:
[Beveiliging paragraaf 2]({{< ref "/posts/beheer/beheer_debian_beveiliging" >}})

### gebruikers

maak een gebruiker en geef het wachtwoord voor de gebruiker:

    useradd -u 1026 -g users -G sudo,audio -c "Stauntonel" -m -d /home/stauntonel -s /bin/bash stauntonel
    password stauntonel

### ssh

Nu is het zaak om root acces uit te schakelen. Ook gaan we wachtwoorden vervangen door keys.
Hoe je dat doet lees je hier: [ssh]({{< ref "/posts/beheer/beheer_ssh" >}}).

Nu kun je het root account locken voor inloggen:

    sudo passwd -l root

### firewall

voor het inrichten van de firewall verwijs ik naar [Uncomplicated Firewall]({{< ref "/posts/beheer/beheer_ufw" >}}).

## 02 webserver

### Nginx

Omdat we later een hugo website gaan inrichten is deze software even voldoende:

    sudo apt install nginx curl

En pas zonodig de firewall aan

    sudo ufw allow "Nginx Full"

En controleer of de webserver loopt.

    curl localhost

Als het goed is wordt je dan begroet door de basis pagina.

### Certificaat

Volg de aanwijzingen op de [Certbot site](https://certbot.eff.org/instructions):

- kies bij software voor Nginx
- kies bij system voor pip

Wel regelmatig certbot bijwerken met :

    sudo /opt/certbot/bin/pip install --upgrade certbot-nginx

[Meer server](/categories/server)
