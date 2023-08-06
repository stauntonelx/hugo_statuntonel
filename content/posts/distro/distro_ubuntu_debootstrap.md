---
title: "Debootstrap installeer Ubuntu"
date: "2021-01-24"
draft: false
author:
description: "Hoe installeer je Ubuntu met behulp van debootstrap"
categories:
  - Distro
tags:
  - ubuntu
  - debootstrap
slug: distro-ubuntu-debootstrap
toc: true
---

Doel is om ubuntu te installeren met debootstrap.
We starten met een willekeurige arch installatie, of van de arch installatie usb.

<!--more-->

Ik ga er van uit dat je al een partitie hebt geformatteerd, zodat we gelijk aan de slag kunnen.

## 01 mount de partitie

    mkdir -p /mnt/debinst
    mount /dev/sda7 /mnt/install

Maak een swap partitie:

    mkswap /dev/sda3
    sync
    swapon /dev/sda3

## 02 Installeer programma's

  pacman -S binutils debootstrap arch-install-scripts ubuntu-keyring

## 03 Installeer Ubuntu

    /usr/sbin/debootstrap --variant=minbase --components=main,restricted,universe\
    ,multiverse --include=nano,dialog,bash-completion,tmux,locales,console-setup\
    ,gpm,tasksel,sudo,tzdata,links,iputils-ping,apt-utils,gnupg,btrfs-progs,dosfstools\
    ,htop,xdg-user-dirs,less,mlocate,autofs,nfs-common,git,iproute2,rfkill,eatmydata\
    ,fonts-terminus --arch amd64 focal /mnt/install https://mirror.nl.leaseweb.net/ubuntu/

### Chroot

    arch-chroot /mnt/install
    export TERM=xterm-color
    source /etc/profile
    source /etc/skel/.bashrc

### symlinks

    cd /media
    mkdir cdrom0
    ln -s cdrom0 cdrom
    cd /
    ln -s media/cdrom

### toetsenbord

    sudo dpkg-reconfigure keyboard-configuration
    sudo dpkg-reconfigure console-setup

### Tijd

    nano /etc/adjtime

bijvoorbeeld:

    0.0 0 0.0
    0
    UTC

dan:

    sudo dpkg-reconfigure tzdata

### locales

    sudo dpkg-reconfigure locales

### kernel

    apt search linux-image
    apt install linux-image-generic-hwe-20.04

### wachtwoord

    passwd

### afronden

Optioneel

    tasksel install standard

opruimen

    apt clean

Om de een of andere reden is de initramfs soms corrupt.
Dan moet je die opnieuw opbouwen met:

    update-initramfs -u -k all

## 04 Installeer debian vervolg

### xorg

Installeer de minimale xubuntu met tasksel

    tasksel install xubuntu-core

aangevuld met:

    apt install -y --no-install-recommends xfonts-terminus geany

### suckless

    apt install -y build-essential libx11-dev libxft-dev libxinerama-dev

### media

    apt install -y --no-install-recommends mpd mpc ncmpcpp beets pulseaudio alsa-utils shotwell

en met alle recommends

    apt install -y gnome-mpv ffmpeg-doc libdvdcss2

[Meer distro installeren](/categories/distro)
