---
title: Beheer schijven
date: 2021-10-10T12:51:47+02:00
draft: true
description: formatteren, versleutelen en veilig verwijderen data
categories:
  - beheer
tags:
  - schijven
  - beveiliging
slug:
  - beheer-schijven-
toc: true
---

Hoe beheer je een harde schijf of ssd in Linux. Aandacht voor veilig verwijderend data, opnieuw partioneren en versleutelen van partities.

<!--more-->

## 01 Veilig verwijderen data

### harde schijf

***Zorg vooraf dat er geen partities gemount zijn!***
Bij een harde schijf kun je het best gebruik maken van de shred utility. Standaard overschrijft het programma de data van gekozen partitie of schijf met random data, die daarna nog eens 4 keer overschreven wordt met nullen.
Een partitie kun je zo veilig overschrijven:

    shred -vfzn 1 /dev/sdb4

Een hele schijf wissen inclusief de partitiontable gaat als volgt:

    shred -vfzn 3 /dev/sdb

**Let op!**
Bij de partitie wordt de data 2 keer overschreven 1 keer met random data en 1 keer met nullen (n 1) bij de schijf gaan we voor 3 keer met nullen. (n 3)

### SSD en NVME

Het veilig wissen van dit soort schijven vraagt om een heel andere aanpak. Dat komt omdat de opslag techniek heel anders werkt dan bij harde schijven. daarom verwijs ik naar de arch wiki voor meer informatie:

[Solid state drive/Memory cell clearing](https://wiki.archlinux.org/title/Solid_state_drive/Memory_cell_clearing)

## 02 inrichten en formateren nieuwe schijf

Voor het formateren van harde schijven gebruik ik meestal fdisk, hieronder een paar aantekeningen die je als naslag kunt gebruiken.

    fdisk /dev/sdb

letter p:
: overzicht van huidige partitie tabel

### gpt partitie tabel

Sterk aan te bevelen bij een UEFI BIOS en windows. UEFI is al heel lang de standaard.

letter g:
: om een nieuwe GPT partitie tabel te maken.

### DOS partitie tabel

Voor oude machines, is dit de standaard als ze nog geen UEFI Bios hebben. Wil je alleen linux installeren dan is dit ook een optie bij nieuwere systemen. Maar nogmaals niet aan te bevelen.

letter o:
: om een nieuwe DOS partitie tabel te maken

### type partites

Letter l:
: opsomming van alle mogelijke partitie types

Meest gebruikte type partities:

|type|omschrijving|uitleg|
|----|-----------|------|
|1|EFI System| nodig voor de bootloader om een UEFI systeem te starten|
|19|SWAP| gebruik je voor SWAP partition (minimaal 1 x geheugen, max 1,5 geheugen) ook gebruikt bij hybernation|
|20|Linux filesysteem|HOME en ROOT partition|
|30|Linux LVM|Gebruiken als je werkt met Logical Volume Management (handig voor snapshots|

Letter n:
: nieuwe partitie toevoegen

De beginsector kun je meestal overnemen.
De eindsector kun je aangeven als vogt:
- +20G partitie wordt 20 Gig groot
- +250M partitie wordt 250 mb
- -50G partitie groeit maar houd 50G vrije ruimte over aan het eind van de schijf

### formateren partitie

Mijn favorite filsysteem is btrfs, ondanks dat het nog volop in ontwikkeling is heb ik nog nooit problemen gehad.
Je kunt ook snapshots maken en werken met pools, die je kunt uitbreiden met meerdere schijven /partities. Subvolumes kun je gebruiken als partities waarop je bijvoorbeeld een distro kunt installeren.

|filesysteem|commando|beschijving|
|-----------|--------|-----------|
|btrfs|mkfs -L BTRFS /dev/sda2|formateer /dev/sda2 als BRTFS met als label: BTRFS|
|fat 32 /vfat|mkfs.vfat -n ESP /dev/sda1|formateer /dev/sda1 met label ESP voor bijvoorbeeld een Uefi partitie|
|ext4|mkfs.ext4 -L DEBIAN /dev/sda3|Formateer /dev/sda4 als ext4 met label DEBIAN|

## 03 dual boot met windows

## 04 versleutel je data

[Meer beheer](/categories/beheer)
