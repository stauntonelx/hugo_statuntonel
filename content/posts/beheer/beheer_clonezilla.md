---
title: "Clonezilla"
date: 2021-02-13T23:02:53+01:00
draft: false
author:
description: "Tips om je partitie /schijf remote te herstellen"
categories:
  - beheer
tags:
  - beveiliging
slug: beheer-beveiliging-clonezilla
---

Tips om je schijf / partitie te herstellen vanaf een clonezilla image op een nas met een NFS server.

<!--more-->

## 01 Bestandrechten

Als je de melding krijgt: "Permission denied" moet je er voor zorgen dat in de map alle rechten openstaan:

    chmod -R 777 map

Doe dit bij voorkeur locaal door in te loggen op de nfs server.

## 02 NFS server

Om te kunnen herstellen heb je allereerst een ip-adres nodig.
Kies niet voor de optie nfs-server maar voor de handmatige installatie in het menu. Je komt dan in een commandprompt en voert de volgende acties uit:

Interface achterhalen

    iw dev

wifi instellen

    ip link set wlan0 up
    wpa_passphrase kamer >> /etc/wpa_supplicant.conf

Type nu je wachtwoord in en dan gaan we wpa_supplicant starten

    wpa_supplicant -B -D wext -i wlan0 -c /etc/wpa_supplicant.conf
    dhclient wlan0

Nu moet je nog wel even de clonezilla map mounten op deze manier

    mount 192.168.178.3:/volume1/data/cz /home/partimage

Vervolgens sluiten we de terminal en kunnen we verder met het herstellen/maken van een backup

[Meer beheer](/categories/beheer)
