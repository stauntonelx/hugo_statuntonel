---
title: "Versleutelde usb installatie"
date: 2021-09-03T14:00:27+02:00
draft: true
description: debian op versleutelde usb
categories:
  - distro
tags:
  - debian
  - versleutelen
slug:
  - usb-versleutelen
toc: true
---

Installeer een distro op een versleutelde usb met grub als bootloader.

<!--more-->

## 01 partitioneren
We willen dat de usb gebruikt kan worden met efi en met bios.
Daarvoor gebruiken we gdisk en kies voor o (nieuwe GPT table).
Dan partitioneren we de disk als volgt:

    Number  Start (sector)    End (sector)  Size     Code  Name
     1            2048           22527   10.0 MiB    EF02  BIOS boot partition
     2           22528          432127   200.0 MiB   EF00  EFI system partition
     3          432128          841727   200.0 MiB   8300  Linux filesystem
     4          841728         7884766   3.4 GiB     8300  Linux filesystem

Partitie 1: bios partitie hoeven we niet te formateren.
Partitie 2: de esp partitie voor EFI systeem (fat)
Partitie 3: de eigenlijke boot partitie (ext2) nodig voor debian
Partitie 4: root partitie, deze gaan we met luks encrypten (ext4)

## 02 encryption
Maak cryptdevice:

    cryptsetup -y -v luksFormat /dev/sdb4

Open het device

    cryptsetup open /dev/sdb4 cryptusb

En maak /etc/crypttab met de volgende inhoud:

    cryptusb UUID=fcccb37e-4b30-4671-8610-be80cb37fb6c none luks

gebruikte de UUID van de root partitie

## 03 formateren
Formateer nu je partities:

    mkfs.fat -F32 -n ESP /dev/sdb2
    mkfs.ext2 -L BOOT /dev/sdb3
    mkfs.ext4 -L ROOT /dev/mapper/cryptusb

Controleer met lsblk:

    lsblk -f

als het goed is ziet het er nu zo uit:

    NAME         FSTYPE      LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
    sdb
    ├─sdb1
    ├─sdb2       vfat        ESP   122B-38A4                             192.1M     2% /boot/efi
    ├─sdb3       ext2        BOOT  e879de6d-13ee-45cc-b184-c2020fe5756a  129.1M    28% /boot
      └─sdb4       crypto_LUKS       fcccb37e-4b30-4671-8610-be80cb37fb6c
      └─cryptusb ext4        ROOT  9b27aec1-3fdb-4bf1-abac-fa8cb6e4690c    1.9G    35% /

## 04 fstab
Na installatie ziet je /etc/fstab er zo uit:

    # /dev/mapper/cryptusb LABEL=ROOT
    UUID=9b27aec1-3fdb-4bf1-abac-fa8cb6e4690c / ext4 rw,relatime 0 1
    # /dev/sdb3 LABEL=BOOT
    UUID=e879de6d-13ee-45cc-b184-c2020fe5756a /boot ext2 rw,relatime 0 2
    # /dev/sdb2 LABEL=ESP
    UUID=122B-38A4 /boot/efi vfat rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,utf8,errors=remount-ro 0 2

## 05 installeer grub

**DEBIAN:**

    apt install grub2 grub-efi-amd64 cryptsetup-initramfs

Installeer grub voor BIOS

    grub-install --target=i386-pc --boot-directory /boot /dev/sdb

Installeer grub voor efi

    grub-install --target=x86_64-efi --efi-directory /boot/efi --boot-directory /boot --removable

Pas de /etc/default/grub aan

    GRUB_CMDLINE_LINUX_DEFAULT="cryptdevice=UUID=fcccb37e-4b30-4671-8610-be80cb37fb6c root=/dev/mapper/cryptusb"

Maak een nieuwe initram:

    update-initramfs -u -k all

update grub:

    grub-mkconfig -o /boot/grub/grub.cfg

[meer distro installeren](/categories/distro)
