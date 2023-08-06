---
title: "Grub en UEFI"
date: 2021-02-10T22:17:59+01:00
draft: false
author:
description: Grub installeren op GTP schijf met UEFI partitie
categories:
  - beheer
tags:
  - arch
  - bootloader
slug: beheer-arch-bootloader-grub
toc: true
---

Gtp schijf partitioneren met fdisk, UEFI partitie formateren en grub installatie na arch installatie

<!--more-->

## 1. uefi

### uefi check:

Om grub te installeren, moet je opstarten vanaf het installatie middel in uefi mode! Check na inloggen met dit commando of dat goed gelukt is:

    efivar -l

Als het goed is krijg je dan een lange lijst met codes.

### Paritioneren

Partitioneren doen we met fdisk

    gdisk /dev/sda

Kies voor optie **'o'** om een nieuwe gtp partitie tabel te maken. Zorg er voor dat de eerste partitie ongeveer 1024M groot is en van het type **'EF00'**

### Formatteren

Formateren kun je op de volgende manier:

    mkfs.fat -F32 /dev/sda1

### Fstab

neem de volgende regel op in je /etc/fstab:

    UUID=DC83-4943 /boot vfat rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro	0 2

De UUID vind je met:

    blkid| grep sda1

## 2. Grub

Na [installeren]({{< ref "/posts/distro/distro_arch" >}} "installeer basis arch zonder X") van Arch kunnen we nu grub configureren

    pacman -Sy grub
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub

Daarna

    grub-mkconfig -o /boot/grub/grub.cfg

[Meer beheer](/categories/beheer)
