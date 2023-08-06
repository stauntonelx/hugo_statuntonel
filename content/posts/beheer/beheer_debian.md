---
title: "Debian beheer"
date: 2021-02-13T22:43:18+01:00
draft: false
author:
description: "Debian beheer tips voor server en desktop"
categories:
  - beheer
tags:
  - debian
slug: beheer-debian
toc: true
---

Debian beheer tips voor server en desktop

<!--more-->

## 01 verwijder oude kernels

Zoek uit welke kernel nu is geinstalleerd, deze willen we namelijk niet verwijderen.

    uname -r

Zoek de headers van geinstalleerde kernels

    dpkg --list | grep linux-image

Daarna kun je oude kernels verwijderen met:

    apt-get --purge remove linux-image-XXX

Dan moeten we grub nog bijwerken met

    update-grub2

Daarna kunnen we testen of alles nog werkt

    reboot

## 02 auto-completion bash

    apt install bash-completion

Als je ook als root gebruik wil maken van auto completion

    nano ~/.bashrc

voeg deze regel toe:

    if [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
    fi

## 03 chroot arch from debian

Mount arch op /mnt/distro

    cd /mnt/distro
    mount -t proc /proc proc/
    mount --rbind /sys sys/
    mount --rbind /dev dev/

optioneel:

    mount --rbind /run run/

Voor internet:

    cp /etc/resolv.conf etc/resolv.conf

en chroot:

    chroot /mnt/distro /bin/bash

## 04 citrix

Op de citrix site kun je de laatste citrix app downloaden.
Die moet je installeren met sudo

    sudo dpkg -i path_to_deb_file

Als het nodig is

    sudo apt install -f

Daarna moet je nog 1 ding doen:

    rm -Rf /opt/Citrix/ICAClient/keystore/cacerts/

en maak een simlink

    sudo ln -s /etc/ssl/certs /opt/Citrix/ICAClient/keystore/cacerts

## 05 default terminal st

Zo maak je st de default terminal

    sudo update-alternatives --install /usr/bin/x-terminal-emulator x-terminal-emulator /usr/local/bin/st 50
    sudo update-alternatives --config x-terminal-emulator

Kies nu voor st als default terminal!

[Meer beheer](/categories/beheer)
