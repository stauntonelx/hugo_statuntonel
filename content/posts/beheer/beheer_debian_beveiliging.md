---
title: "Debian beveiliging"
date: 2021-02-13T21:25:09+01:00
lraft: false
author:
description: "Extra beveiliging op debian server en desktop"
categories:
  - beheer
tags:
  - debian
  - beveiliging
slug: beheer-debian-beveiliging
toc: true
---

Extra beveiligingstips voor debian server of desktop.

<!--more-->

## 01 lock root

Alle gebruikers die kunnen inloggen, zijn natuurlijk per definitie een beveiligings issue. Daarom locken we het root account met:

    sudo passwd -l root

Mocht je spijt krijgen dan kun je altijd het account weer vrijgeven met:

    sudo passwd

In de tussentijd kun je natuurijk nog wel root zijn met:

    sudo su

## 02 Umask

Bij Debian is de standaard umask ingesteld op 022. Elk bestand krijgt standaard 777 als rechten. Met een umask van 022 wordt dat 777 - 022 = 755. Voor een server adviseer ik een umask van 027. Dat regel je door de volgende bestanden aan te passen:

    sudo nano /etc/pam.d/common-session

voeg de volgende regel toe als die niet al opgenomen is.

    session optional pam_umask.so

Verder moet we dit bestand aanpassen

    sudo nano /etc/login.defs

Verander de regel

    UMASK 027

[Meer beheer](/categories/beheer)
