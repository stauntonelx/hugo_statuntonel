---
title: "Alpine installeren"
date: 2021-08-29T21:33:17+02:00
draft: false
description: Installeer alpine op btrfs subvolume
categories:
- Distro
tags:
- alpine
- btrfs
slug:
- distro-alpine-btrfs
toc: true
---

Installeer alpine vanaf een live usb, verander standaard bootloader naar grub.

<!--more-->


## 01 usb maken

Download de standaard iso en kopieer naar usb met dd.

    dd if=alpine.iso of=/dev/sdx && sync

## 02 setup alpine

Doorloop de alpine setup en skip de laatste stap. (installatie naar disk overslaan)

    setup-alpine

## 03 grub

Standaard gebruikt alpine syslinux, wij willen grub.

    export BOOTLOADER=“grub”
    apk add grub

## 04 btrfs

Omdat we alpine op een btrfs volume willen installeren moeten we het eerst installeren.

    apk add btrfs-progs

Mount de btrfs partitie.

    Mount -t btrfs /dev/sda2 /mnt

Creeer nu het subvolume

    cd /mnt
    Btrfs subvolume create 03_alpine

Unmount partitie

    cd /
    umount /dev/sda2

en mount subvolume

    mount -t btrfs -o subvol=03_alpine /dev/sda2 /mnt

## 05 installeer naar disk

Nu moeten we nog alles naar disk installeren

    setup-disk -v /mnt

[Meer distro installeren](/categories/distro)
