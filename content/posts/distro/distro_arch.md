---
weight: 200
title: Arch basis installatie
date: "2021-01-24"
draft: false
author:
description: Arch installatie vanuit arch / instalatie medium
categories:
  - Distro
tags:
  - arch
slug: distro-arch-basis
toc: true
---

Installeer een basis arch vanuit een bestaande arch installatie of vanaf het installatie medium.

<!--more-->

## 01 Installeren basis Arch distro

tijdzone instellen

    timedatectl set-timezone Europe/Amsterdam
    timedatectl set-ntp true

pas de mirrorlist aan en zet nederlandse / Duitse servers voorop

    nano /etc/pacman.d/mirrorlist

als je installleert vanuit een bestaande arch installatie:

    pacman -Syu
    pacman -S arch-install-scripts

Voor nu ga ik er van uit dat de installatie partitie al is aangemaakt, formateer de partitie:

    mkfs.ext4 -L testarch /dev/sda3

check filesystem

    fsck.ext4 -fn /dev/sda3

mount partitie

    mount /dev/sda3 /mnt/install

installeer de basis arch met pacstrap

    pacstrap -i /mnt/install base linux linux-firmware bash-completion nano

En genereer een fstab:

    genfstab -Up /mnt/install >> /mnt/install/etc/fstab

check fstab

    lsblk -f
    nano /mnt/install/etc/fstab

## 02 Chroot nieuwe installatie

    arch-chroot /mnt/install

extra software installeren

    pacman -S tmux gpm links sudo dosfstools btrfs-progs hdparm autofs nfs-utils openssh \
    reflector ntfs-3g wget iwd git tree rsync wget aria2

aanpassen locale

    nano /etc/locale.gen
    locale-gen
    locale |grep LANG= > /etc/locale.conf

maak ramdisk

    mkinitcpio -p linux

root wachtwoord

    passwd

user stauntonel

    useradd -u 1026 -g users -G wheel,audio -c "Stauntonel" -m -d /home/stauntonel -s /bin/bash stauntonel
    passwd stauntonel

configure sudo

    export EDITOR=nano
    visudo

uncomment:

    %wheel ALL=(ALL) ALL

systemd services

    systemctl enable iwd.service systemd-resolved.service

stel iwd als dhcp server en systemd voor nameresolving:

    mkidr /etc/iwd
    nano /etc/iwd/main.conf

geef dit bestand de volgende inhoud:

    [General]
    EnableNetworkConfiguration=true

    [Network]
    NameResolvingService=systemd

mountdirectories

    mkdir -p /mnt/nas

installeer grub voor BIOS partitie

    pacman -S grub
    grub-install --target=i386-pc /dev/sda

installeer grub voor EFI partitie

    grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB

grub bijwerken van huidige Arch map

    grub-mkconfig -o /boot/grub/grub.cfg

Voor efi kun je ook systemd-boot gebruiken, daarvoor heb je geen extra software nodig

unmount chroot

    exit
    umount -R /mnt

## 03 reboot in nieuwe omgeving

Op mijn asus heb ik een spinnig probleem met de harddisk. Om dat op te lossen gebruiK:

    hdparm -B 254 /dev/sda

Maak een bestand /etc/udev/rules.d/50-hdparm.rules<br>
met de volgende inhoud:

    ACTION=="add", SUBSYSTEM=="block", KERNEL=="sda", RUN+="/usr/bin/hdparm -B 254 -S 0 /dev/sda"

hostname

    hostnamectl set-hostname myhostname

hostname toevoegen aan hosts

    nano /etc/hosts

tijdzone instellen

    timedatectl set-timezone Europe/Amsterdam
    timedatectl set-ntp true

Check of alles gelukt is

    timedatectl

wifi instellen

    nmtui

activeer juiste netwerk

check of nfs shares bereikbaar zijn:

    mount -t nfs 192.168.178.28:/volume1/data/linux /media/install/

check welke shares aanwezig zijn

    showmount -e 192.168.178.3

netwerk checken of module is geinstallleerd
driver huiskamer: Kernel driver: r8169

    lspci -v
    dmesg | grep r8169
    ip link
    ping -c 3 google.nl

mirrorlist optimaliseren

    cp -vf /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
    reflector --verbose --country 'Netherlands' -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist

autofs starten
copieer alle autofs [bestanden](https://drive.google.com/open?id=0BwyOuSW748DOZDJYNjNrallUdWc) naar /etc/autofs

    systemctl enable autofs
    systemctl start autofs

alsa

    pacman -S alsa-utils

Voor de asus laptop:
maak een bestand aan in de map:<br>
/etc/modprobe.d<br>
met de volgende inhoud:

    options snd-hda-intel id=PCH index=0
    options snd-hda-intel id=HDMI index=1

Check of de kanalen niet dicht staan, en voer een speakertest uit:

    alsamixer 		## unmuting all channels met m-toets
    speaker-test -c 2	## test speakers

als pacman zeurt over keyring:

    pacman-key --init
    pacman-key --populate archlinux

## 04 Bookmarks

[Arch install guide](https://wiki.archlinux.org/index.php/Installation_guide) | [Youtube install guide](https://youtu.be/5P1B1IzAhdo)

[Meer distro installeren](/categories/distro)
