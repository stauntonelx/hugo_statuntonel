---
title: "Systemd"
date: 2021-02-13T23:59:23+01:00
draft: false
author:
description: Netwerk verbinding met systemd en iwd
categories:
  - project
tags:
  - systemd
  - netwerk
slug: project-systemd-netwerk-iwd
toc: true
---

Hoe krijg je netwerk verbinding met alleen iwd en systemd.

<!--more-->

## 01 Services

### Networking

Disable networkmanager en dhcpcd services als dat van toepassing is. Later kunnen ze verwijderd worden.
station wlp3s0 scan
station wlp3s0 get-networks
station wlp3s0 connect Ziggo07744
quit

    sudo systemctl disable NetworkManager
    sudo systemctl disable dhcpcd

Daarna enable de volgende services:

    sudo systemctl enable systemd-networkd
    sudo systemctl enable systemd-resolved

Systemd heeft zijn eigen resolve file, en de oude kan dus verwijderd worden. Sommige programma's hebben dit bestand nodig, dus vervangen we het door een link.

    sudo rm /etc/resolv.conf
    sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

Systemd heeft wel config files nodig.

## 02 Bekabeld ##

met het volgende comando kun je de netwerk interfaces achterhalen:

    ip -a

Daarna maken we een config aan met:

    nano /etc/systemd/network/50-wired.network

en geef het de volgende inhoud:

    Name=enp*

    [Network]
    DHCP=ipv4

    [DHCP]
    RouteMetric=10

## 03 draadloos ##

Ook voor wireless maken we een config file.

    nano /etc/systemd/network/25-wireless.network

en geef het de volgende inhoud:

    Name=wlp3s0

    [Network]
    DHCP=ipv4

    [DHCP]
    RouteMetric=20

Een aparte dhcpcd service is nu niet meer nodig,je krijgt je ip direct van systemd!
Start de services met

    sudo systemctl enable systemd-networkd
    sudo systemctl enable systemd-resolved

## 04 iwd instellen

Ook de draadloos interface krijgen we aan de praat. Daarvoor hebben we iwd nodig:

    pacman -S iwd
    systemctl enable iwd
    systemctl start iwd

Daarna kun je de instellingen vastleggen met het commando iwctl.
List de interfaces met

    iwctl
    device list

Scan de netwerken met

    station wlp3s0 scan
    station wlp3s0 get-networks
    station wlp3s0 connect Ziggo07744
    quit

Geef je wachtwoord in als hier omgevraagd wordt.
De instellingen worden bewaard in /var/lib/iwd.
De volgende keer zal automatisch geconnect worden met het laastse netwerk.

[Arch systemd-networkd](https://wiki.archlinux.org/index.php/Systemd-networkd)

[meer projecten](/categories/project)
