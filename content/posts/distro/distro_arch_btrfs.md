---
title: "Arch installeren op BTRFS"
date: "2021-01-26T23:25:04+01:00"
draft: false
author:
description: "Installeer Xorg en XFCE4 na een basis intallatie Arch"
categories:
  - Distro
tags:
  - arch
  - btrfs
slug: distro-arch-btrfs
toc: true
---

**BTRFS** is een filesysteem waarbij je heel simpel snapshots kunt maken.Snapshots kunnen ook weer als een soort partitie gebruikt worden. Daarmee maak je heel efficent gebruik van je schijfruimte.

<!--more-->

## 01 Partitioneren
Omdat de meeste bootloaders nog geen btrfs ondersteunen kunnen we die het best op bijvoorbeeld ext4 formateren. Ook ondersteunt btrfs geen swap dus die houden we ook apart.

Partitioneer de disk:

    fdisk /dev/sda

Maak 2 partities en formateer ze.

    mkfs.ext4 -L boot /dev/sda1
    mkfs.btrfs -L butter /dev/sda2

## 02 Mount btrfs partitie

    mount /dev/sda2 /mnt
    cd /mnt
    btrfs subvolume create @
    cd @
    btrfs subvolume create @arch
    cd /
    umount /dev/sda2
    mount -o noatime,compress=lzo,space_cache=v2,subvol=@/@arch /dev/sda2 /mnt
    cd /mnt
    mkdir boot
    mount /dev/sda1 /mnt/boot

## 03 Installeer arch

    pacstrap /mnt base base-devel btrfs-progs
    genfstab -U -p /mnt >> /mnt/etc/fstab
    nano /mnt/etc/mkinitcpio.conf

De volgende hooks heb je nodig:

    HOOKS="base udev autodetect modconf block btrfs filesystems keyboard"

Filecheck uitgeschakeld btrfs ondersteund dit niet en btrfs voor de "filesystems" hook.

[Meer distro installeren](/categories/distro)
