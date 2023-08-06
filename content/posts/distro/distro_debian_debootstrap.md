---
weight: 100
title: "Debootstrap Installeer Debian"
date: "2022-06-30"
author:
draft: false
description: "Hoe installeer je Debian met behulp van debootstrap"
categories:
  - Distro
tags:
  - debian
  - debootstrap
slug: distro-debian-debootstrap
toc: true
---

Het doel is om debian te installeren met debootstrap. We starten met een willekeurige arch installatie, of van het arch installatie medium.

<!--more-->

Ik ga er van uit dat je al een partitie hebt geformatteerd, zodat we gelijk aan de slag kunnen.

## 01 mount de partitie

```
mkdir -p /mnt/debinst
mount /dev/sda7 /mnt/debinst
```

Maak een swap partitie:

```
mkswap /dev/sda3
sync
swapon /dev/sda3
```

## 02 Installeer programma's

```
pacman -S binutils debootstrap arch-install-scripts
```

## 03 Installeer debian

```
/usr/sbin/debootstrap --variant=minbase --components=main,contrib,non-free,non-free-firmware \
--include=nano,dialog,bash-completion,tmux,locales,console-setup,tasksel,\
dosfstools,btrfs-progs,links,network-manager,sudo,git,tree,cryptsetup,cryptsetup-initramfs \
--arch amd64 bookworm /mnt/debinst http://ftp.nl.debian.org/debian/
```

### Chroot

```
arch-chroot /mnt/debinst
export TERM=xterm-color
source /etc/profile
source /etc/skel/.bashrc
```

### Tijd

```
nano /etc/adjtime
```

geef dit bestand de volgende inhoud:

```
0.0 0 0.0
0
UTC
```

dan:

```
dpkg-reconfigure tzdata
```

### locales

```
dpkg-reconfigure locales
```

### toetsenbord

```
dpkg-reconfigure keyboard-configuration
```

Voor goede utf ondersteuning

```
dpkg-reconfigure console-setup
```

### sources.list

```
nano /etc/apt/sources.list
```

Zorg dat deze er als volg uitziet:

```
deb http://ftp.nl.debian.org/debian bookworm main contrib non-free non-free-firmware
deb http://deb.debian.org/debian/ bookworm-updates main contrib non-free
deb http://deb.debian.org/debian-security bookworm-security main contrib non-free
```

Daarna

```
apt update && apt upgrade
```

### standaard software

```
tasksel install standard laptop
```

### firmware

```
apt install -y firmware-linux-nonfree firmware-realtek firmware-iwlwifi bluez-firmware rfkill
```

### kernel

```
apt install -y linux-image-amd64
```

### wachtwoord

```
passwd
```

### gebruiker opvoeren

```
useradd -u 1026 -g users -G sudo,audio -c "Stauntonel" -m -d /home/stauntonel -s /bin/bash stauntonel
passwd stauntonel
```

### afronden

opruimen cache (optioneel)

```
apt clean
```

Om de een of andere reden is de initramfs soms corrupt. Dan moet je die opnieuw opbouwen met:

```
update-initramfs -u -k all
```

## 04 Installeer debian vervolg

### desktop

```
apt install -y --no-install-recommends \
task-xfce-desktop xfce4-terminal mousepad xfce4-power-manager-plugins xfce4-whiskermenu-plugin gnome-themes-extra papirus-icon-theme xfce4-notifyd gvfs gvfs-backends gvfs-fuse thunar-archive-plugin thunar-volman mugshot policykit-1-gnome light-locker menulibre dbus-x11 plymouth plymouth-themes network-manager-gnome pavucontrol gnome-keyring blueman
```

firefox met alle recommends:

```
apt install -y firefox-esr
```

### suckless

```
apt install -y build-essential libx11-dev libxft-dev libxext-dev libxinerama-dev
```

### media

```
apt install -y --no-install-recommends \
mpd mpc ncmpcpp beets pulseaudio pulseaudio-module-bluetooth
```

en met alle recommends

```
apt install -y parole lollypop shotwell
```

### fstab in orde maken

Mount eerst alle partities en ga dan uit de chroot

```
exit
```

Dan gaan genereren we een fstab met

```
genfstab -Up /mnt/debinst >> /mnt/debinst/etc/fstab
```

### bootloader

Installeer een [bootloader](/tags/bootloader) naar keuze.

[Meer distro installeren](/categories/distro)
