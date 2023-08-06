---
title: "Gentoo installatie"
date: 2021-08-29T21:53:42+02:00
draft: true
description: basis installatie van gentoo op btrfs
categories:
  - Distro
tags:
  - gentoo
  - btrfs
slug:
  - distro-gentoo-basis
toc: true
---

Installeren Gentoo op BTRFS vanuit een arch distributie.

<!--more-->

## 01 BTRFS subvolume maken

De btrfs partitie bevind zich in dit geval op /dev/sda4. Al mijn subvolumes zijn gemount in de map /btrfs. In deze map is een subvolume aangemaakt voor de root volumes van alle geinstalleerde distributies. Maak een nieuw subvolume aan voor de root van Gentoo:

    btrfs subvolume create /btrfs/@/@gentoo

### Mount

Mount dit subvolume als volgt:

    mkdir -p /mnt/gentoo
    mount -o noatime,comress=lzo,space_cache,subvol=/@/@gentoo /dev/sda4 /mnt/gentoo
    cd /mnt/gentoo

### Download

Nu moeten we de stage3 tarball downloaden via een Nederlandse [mirror](https://www.gentoo.org/downloads/mirrors/#NL): Let op dat je de juiste architectuur kies, en de voor de systemd versie.

    wget http://mirror.leaseweb.com/gentoo/releases/amd64/autobuilds/current-stage3-amd64-systemd/stage3-amd64-systemd-20180103.tar.bz2
    wget http://mirror.leaseweb.com/gentoo/releases/amd64/autobuilds/current-stage3-amd64-systemd/stage3-amd64-systemd-20180103.tar.bz2.DIGESTS

check de checksum:

    sha512sum stage3-amd64-systemd-20180103.tar.bz2

### Uitpakken

    tar xpf stage3-amd64-systemd-20180103.tar.bz2 --xattrs-include='*.*' --numeric-owner
    rm stage3*

### Compileer instellingen

De compileer instellingen kun je vastleggen met:

    nano -w /mnt/gentoo/etc/portage/make.conf

Pas het aantal cores aan (werkelijk aantal +1)

    MAKEOPTS="-j5"

De architectuur laten we automatisch bepalen door gcc

    CFLAGS="-march=native -O2 -pipe"

De global useflags zijn belangrijk voor de compilatie, let op dat je deze niet te uitgebreid maakt!

    USE="-consolekit -kde -qt4 -qt5 btrfs ffmpeg gnome gtk lzo policykit systemd"

Kies een 2-tal mirrors

    GENTOO_MIRRORS="http://ftp.snt.utwente.nl/pub/os/linux/gentoo/ http://mirror.leaseweb.com/gentoo/"

### portage

Het is aangeraden de standaard instellingen niet aan te passen.

    mkdir --parents /mnt/gentoo/etc/portage/repos.conf
    cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf

## 02 chroot

Nu kunnen we de omgeving in met:

    mount -t proc none /mnt/gentoo/proc
    mount --rbind /sys /mnt/gentoo/sys
    mount --make-rslave /mnt/gentoo/sys
    mount --rbind /dev /mnt/gentoo/dev
    mount --make-rslave /mnt/gentoo/dev
    cp /etc/resolv.conf etc
    chroot /mnt/gentoo /bin/bash
    source /etc/profile

### Tijdzone instellen

    echo "Europe/Amsterdam" > /etc/timezone
    emerge --config sys-libs/timezone-data

### Locale

    nano /etc/locale.gen
    locale-gen
    eselect locale list
    eselect locale set
    env-update && source /etc/profile

[Meer distro installeren](/categories/distro)
