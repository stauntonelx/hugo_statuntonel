---
title: "Docker installeren debian"
date: 2021-07-15T20:15:53+02:00
draft: false
author:
description:
  Installeren docker onder debian en verander data locatie
categories:
  - project
tags:
  - debian
  - docker
slug: project-debian-docker
toc: true
---

Hoe installeer je docker onder debian.

<!--more-->

## 01 Installeren

Eerst gaan we de repos bijwerkern:

    sudo apt update

Daarna installeren we een paar extra programma's

    sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common

Dan voegen we de GPG key van de docker repo toe.

    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

voeg de repo toe

    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable"

dan opnieuw een update

    sudo apt update

check of je van de docker repo gaat installeren:

    apt-cache policy docker-ce

Je krijgt dan een lijst met installeerbare versies. Op dit moment is dat bijv. 5:19.03.10~3-0~debian-buster.

Daarna kunnen we installeren

    sudo apt install docker-ce

## 02 basislocatie aanpassen

Omdat ik op mijn lapt niet veel schijfruimte heb, wil ik de basis map voor docker verplaatsen naar een externe harddisk
Maak een config file aan voor dockerd

    sudo nano /etc/docker/daemon.json

En geef het de volgende inhoud

    {
        "data-root": "/mnt/docker-data",
        "storage-driver": "overlay2"
    }

## 03 Rechten nieuwe doelmap

Nu moeten we nog de rechten goed zetten:

    chown -R root:root /mnt/docker-data
    chmod 711 /mnt/docker-data

## 04 kopieer oude locatie naar nieuwe

Net rsync kopieren we de oude map naar de nieuwe locatie

    rsync -aqxP /var/lib/docker/ /mnt/docker-data

[meer projecten](/categories/project)
