---
title: "Systemd Boot"
date: 2021-02-13T21:05:58+01:00
draft: false
author:
description: systemd-boot inrichten op btrfs
categories:
  - beheer
tags:
  - arch
  - bootloader
  - btrfs
slug: beheer-arch-bootloader-systemd
toc: true
---

Systemd-boot installeren en inrichten, na partitioneren gtp-schijf met UEFI en btrfs partitie.

<!--more-->

## 01 Partitioneren

Voor systemd-boot kun je het beste gebruik maken van GPT.
Een voorbeeld van een partitieschema:

    Device       Start      End  Sectors Size Type
    /dev/sda1     2048  2099199  2097152   1G EFI System
    /dev/sda2  2099200 41943006 39843807  19G Linux filesystem

maak de filesystemen:

    mkfs.vfat -F32 -n ESP /dev/sda1
    mkfs.btrfs -L butter /dev/sda2

zet de bootflag aan met parted:

    parted /dev/sda
    set 1 boot on
    q

## 02 ESP

Als we de bootloader van sysemd zelf gebruiken hoeven we niets extra te installeren.
Wel moeten we er voor zorgen dat het esp (mountpoint voor de efi is vastgelegd in fstab.
Mount eerst de root partitie in dit geval met

    mount -o noatime,compress=lzo,space_cache,subvol=@arch /dev/sda2 /mnt

daarna zorg je ervoor dat de esp wordt gemount;

    cd /mnt
    mkdir -p mnt/esp
    mount /dev/sda1 mnt/esp

Daarna kun je met pactrap arch installeren zoals altijd

    pacstrap /mnt base base-devel dosfstools btrfs-progs
    genfstab -U -p /mnt >> /mnt/etc/fstab

zie verder de installatie aantekeningen

## 03 bootloader

Na de installatie wordt het tijd voor de installatie van de bootloader.
Het kan soms nodig zijn om de vfat module te downloaden

    nano /etc/modules-load.d/vfat

geef het bestan de volgende inhoud:

    vfat

daarna kan de bootloader geinstalleerd worden met:

    bootctl --path=/mnt/esp install
    cd /mnt/esp
    mkdir -p arch/testing

dan gaan we de bootloader zelf configureren:

    cd /mnt/esp/loader
    nano loader.conf

Geef dit betand de volgende inhoud:

    default	arch
    timeout	4
    editor	0

Daarna gaan we bootenties zelf inrichten:

    cd /mnt/esp/loader/entries
    nano arch.conf

Geef dit bestand de volgende inhoud:

    title          Arch Linux
    linux          /arch/testing/vmlinuz-linux
    initrd         /arch/testing/initramfs-linux.img
    options        root=PARTUUID=afe48ccd-024a-4970-a36c-f4275eb28d33 rw rootflags=subvol=@arch

De boot bestanden copieren we naar /mnt/esp/arch/testing

    mv /boot/* /mnt/esp/arch/testing

Vervolgens voegen we deze regel toe in de fstab:

    /mnt/esp/arch/testing /boot none defaults,bind 0 0

mount alle partities:

    mount -a

daarna kunnen de initramfs opnieuw aanmaken met:

    mkinitcpio -p linux

Exit de chroot en start je nieuw geinstalleerde systeem!

[Meer beheer](/categories/beheer)
