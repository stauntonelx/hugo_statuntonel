---
title: "Ubuntu ssh"
date: 2021-02-10T22:05:51+01:00
draft: false
author:
description: hardening ssh op server en client
categories:
  - beheer
tags:
  - ubuntu
  - beveiliging
slug: beheer-ubuntu-beveiliging-ssh
toc: true
---

Ssh inrichten op ubuntu server en client zodat alleen met key en als gebruiker kan worden ingelogt.

<!--more-->

## 01 ssh server

Installeren op Ubuntu server lts 2020.04

    apt-get install openssh-server

Edit de configfile:

    cp /etc/ssh/sshd_config /etc/ssh/sshd_config_org
    nano /etc/ssh/sshd_config

In de config file zorg je dat de volgende regels zijn aangepast:

    PermitRootLogin no
    PubkeyAuthentication yes
    ClientAliveInterval 60
    TCPKeepAlive yes
    ClientAliveCountMax 5

Restart ssh deamon:

    systemctl restart sshd

Check de status:

    systemctl status sshd

Zorg dat er een gebruiker is die je kunt gebruiken om ssh te starten.
De gid en uid kun je het best laten aansluiten met de gid en uid op de client, dat maakt het makkelijker om de rechten over te nemen via rsync

    useradd -u 1000 -g 100 -G 29,27 -c "Stauntonel" -m -d /home/stauntonel -s /bin/bash stauntonel
    passwd stauntonel
    id stauntonel

output:

    uid=1000(stauntonel) gid=100(users) groups=100(users),27(sudo),29(audio)

check of je gebruiker sudo rechten heeft:

    visudo

## 02 ssh client

check de de gid en uid

    id stauntonel

genereer een key met:

    ssh-keygen -f ~/.ssh/id_virtualbox

kopieer je id naar de server:

    ssh-copy-id -i ~/.ssh/id_virtualbox stauntonel@sshserver

pas je ssh config aan:

    nano ~/.ssh/config

Zet de volgende regels in de config

    VisualHostKey=yes
    host vb
    Hostname sshserver
    User stauntonel
    IdentityFile ~/.ssh/id_virtualbox

Pas de volgende aanpassingen aan in

    nano /etc/ssh/ssh_config
    Host *
    ServerAliveInterval 100

probeer in te loggen

    ssh vb

## 03 terug op de server

pas de config opnieuw aan

    nano /etc/ssh/sshd_config

pas de volgende regel aan

    PasswordAuthentication no

Restart ssh deamon:

    systemctl restart sshd

Check de status:

    systemctl status sshd

[Meer beheer](/categories/beheer)
