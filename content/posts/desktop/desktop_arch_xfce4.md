---
title: "arch XFCE4"
date: "2021-01-24"
draft: false
author:
description: "Installeer Xorg en XFCE4 na een basis intallatie Arch"
categories:
  - desktop
tags:
  - arch
slug: desktop-arch-xfce4
toc: true
---

Installeer Xorg en XFCE4 na een basis intallatie Arch.

<!--more-->

## 01 Xorg

Op de asus laptop:

    pacman -S xorg-server xorg-apps xorg-xinit xf86-input-wacom \
    xf86-video-intel xf86-video-fbdev xf86-video-vesa mesa

De xorg servers die je niet gebruikt kun je overslaan bij installatie.

Screentearing ga je tegen met:

    nano /etc/X11/xorg.conf.d/20-intel.conf

Geef het bestand de volgende inhoud

    Section "Device"
    Identifier"Intel Graphics"
    Driver"intel"
    Option"TearFree" "true"
    EndSection

## 02 XFCE4

    pacman -S xfce4 xfce4-power-manager xfce4-whiskermenu-plugin xfce4-notifyd \
    numix-gtk-theme gtk-engine-murrine xfce4-sensors-plugin xfce4-pulseaudio-plugin \
    pavucontrol gvfs gst-libav gvfs-google gnome-keyring gnome-session leafpad firefox \
    noto-fonts

Om gebruik te maken van gnome servixes moet je die aanzetten settings > session and startup> andvanced

Voor de sensorsplugin:

    sensors-detect --auto

## 03 yaourt

    cd Git/
    mkdir yaourt
    cd yaourt/
    git clone https://aur.archlinux.org/package-query.git
    cd package-query
    makepkg -si
    cd ..
    git clone https://aur.archlinux.org/yaourt.git
    cd yaourt
    makepkg -si

Daarna kun je bijvoorbeeld het moka icontheme installeren met:

    yaourt moka

## 04 Lightdm

    pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings light-locker accountsservice

Plaats een achtergrond in:

    /usr/share/pixmaps/

Voor een avator plaats het volgende tekstbestand

    nano /var/lib/AccountsService/users/stauntonel

En geef dit bestand de volgende inhoud:

    [User]
    Language=en_US.utf8
    XSession=xfce
    Icon=/var/lib/AccountsService/icons/stauntonel
    SystemAccount=false

Daarna een png-bestand downloaden van 96x96 pixcels, en plaatsen in

    /var/lib/AccountsService/icons/

De naam aanpassen naar de gebruikersnaam zonder bestandsextensie

    stauntonel

Met lightdm-gtk-greeter-settings kun je lightdm verder tweaken.\\
De config kun je bekijken met:

    ligthdm --show-config

Wil je bij het inloggen een gebruiker kiezen dan kan dat met:

    greeter-hide-users=false

## 05 Whiskermenu
Om lightlocker te gebruiken kun je het volgende commando toevoegen onder "commands"
lock screen:

    dm-tool lock

switch user:

    dm-tool switch-to-greeter

## 06 Firefox

    nano /usr/share/applications/mimeinfo.cache
    inode/directory=Thunar-folder-handler.desktop

## 07 Virtualbox
Op de Host:

    pacman -S virtualbox virtualbox-host-dkms virtualbox-guest-iso
    usermod -a -G vboxusers stauntonel

Zorg wel dat de kernelheaders van de ginstalleerde kernel zijn geinstalleerd

Maak een aparte directory voor Virtualbox

    mkdir -p /home/stauntonel/Virtualbox

voor btrfs:

    chattr +C /home/stauntonel/Virtualbox

Kernel en virtualbox packages niet meenemen bij updates.
Het is echt niet nodig om elke nieuwe kernel upgrade uit te voeren.

    nano /etc/pacman.conf

pas de volgende regel aan

    IgnorePkg = linux-lts linux-lts-headers virtualbox virtualbox-host-dkms virtualbox-guest-iso

Daarna opnieuw systeem starten en virtualbox is klaar voor gebruik

    reboot

[Meer desktop](/categories/desktop)
