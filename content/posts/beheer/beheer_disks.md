---
title: Beheer schijven
date: 2021-10-10T12:51:47+02:00
draft: false
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

## 02 partitioneren

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

## 03 versleutelen

Envrypt je partitie met luks

### tools

In debian hebben we de volgende tools nodig om een partitie te encrypten en om te booten van deze partitie:

    sudo apt install cryptsetup cryptsetup-initramfs 


### versleutel partitie en open

Versleutel je partitie met:

    cryptsetup -y -v luksFormat /dev/sdb3

Open de versleutelde partitie als bijvoorbeeld "cryptbtrfs"

    sudo cryptsetup open /dev/sdb3 cryptbtrfs

Je partitie schema kan er bijvoorbeeld zo uitzien:

    sdb                                             8:16   0 465.8G  0 disk  
    ├─sdb1                                          8:17   0  1024M  0 part  
    ├─sdb2                                          8:18   0 114.7G  0 part
    └─sdb3                                          8:19   0   350G  0 part  
      └─cryptbtrfs                                254:1    0   350G  0 crypt

Het versleutelde block device heeft nu de volgende naam:

    /dev/mapper/cryptbtrfs

Formateer dit met elk gewenst filesysteem, al ligt het voor de hand dat we hier kiezen voor btrfs.

## 04 formateren partities

Mijn favorite filsysteem is btrfs, ondanks dat het nog volop in ontwikkeling is heb ik nog nooit problemen gehad.
Je kunt ook snapshots maken en werken met pools, die je kunt uitbreiden met meerdere schijven /partities. Subvolumes kun je gebruiken als partities waarop je bijvoorbeeld een distro kunt installeren.

|filesysteem|commando|beschijving|
|-----------|--------|-----------|
|btrfs|mkfs -L CRYPTBTRFS /dev/mapper/cryptbtrfs|formateer als BRTFS met als label: CRYPTBTRFS|
|fat 32 /vfat|mkfs.vfat -n ESP /dev/sdb1|formateer /dev/sdb1 met label ESP voor bijvoorbeeld een Uefi partitie|
|ext4|mkfs.ext4 -L BACKUP /dev/sdb2|Formateer als ext4 met label: BACKUP

De btrfs partitie kun je mounten en daarna subvolumes aanmaken met

    sudo mount -o /dev/mapper/btrfs /mnt
    sudo btrfs subvolume create @

## 05 auto mount encrypted partitie

Bepaal eerst de UUID van /dev/sdb3, de partitie die het versleutelde block device bevat.

    sudo blkid -s UUID -o value /dev/sdb3

De output is bijvoorbeeld:

    b2bd8f53-53b8-4264-a547-717cb1fca84f

geef dan het bestand /etc/crypttab de volgende inhoud:

    # <target name> <source device>         <key file>      <options>
    cryptbtrfs UUID=b2bd8f53-53b8-4264-a547-717cb1fca84f none luks

Bij het booten van het systeem zal nu het wachtwoord gevraagd worden en daarna kun je /dev/mapper/cryptbtrfs opnemen in fstab als een normale partitie. De UUID kun je achterhalen door:  

    sudo blkid -s UUID -o value /dev/mapper/cryptbtrfs

in dit geval krijg je dan bijvoorbeeld:

    2fa0012a-7885-44de-9265-4d4c5581c348

je fstab ziet er vervolgens uit als:

    # <file system> <mount point>   <type>  <options>       <dump>  <pass>
    # /dev/mapper/cryptbtrfs LABEL=BTRFS
    
    UUID=2fa0012a-7885-44de-9265-4d4c5581c348	/         	btrfs     	rw,noatime,compress=zstd:3,ssd,space_cache,subvolid=434,subvol=/@,subvol=@	0 0

In dit geval booten we van een subvolume @, dat moet je na het formateren natuurlijk eerst aanmaken.
Ook @home kan gemount worden als homepartitie. We kiezen deze namen om gebruik te kunnen maken van Timeshift.

## 06 systemd-boot

### mount efi

Mount eerst je efi partitie onder /boot/efi

    # /dev/sdb1 LABEL=ESP
    UUID=8442-0FFA      	/boot/efi 	vfat      	rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro	0 2 

### installeer systemd-boot

Daarna kun je systemd-boot installeren met

    apt install systemd-boot

Vanaf bookworm wordt nu vanzef je esp gevuld met de benodigde entries en bootbestanden:

    title      Debian GNU/Linux 12 (bookworm)
    version    6.1.0-10-amd64
    machine-id eda48f1b144f40bb851790fd4f7e0c5b
    sort-key   debian
    options    root=UUID=2fa0012a-7885-44de-9265-4d4c5581c348 rw quit loglevel=3 udev.log_level=3 splash rootflags=subvol=@ systemd.machine_id=eda48f1b144f40bb851790fd4f7e0c5b
    linux      /eda48f1b144f40bb851790fd4f7e0c5b/6.1.0-10-amd64/linux
    initrd     /eda48f1b144f40bb851790fd4f7e0c5b/6.1.0-10-amd64/initrd.img-6.1.0-10-amd64
    

[Meer beheer](/categories/beheer)
