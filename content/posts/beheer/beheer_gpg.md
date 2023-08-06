---
title: "gpg"
date: 2021-07-04T17:35:07+02:00
draft: false
author:
description: versleutel en signeer je data met GPG
categories:
  - beheer
tags:
  - beveiliging
slug: beheer-beveiliging
toc: true
---
GPG is een tool waarmee je bestanden kunt versleutelen/ontsleutelen, maar ook kunt ondertekenen of de ondertekening kunnen checken.

<!--more-->

## 01 verifieer je gedownloade software.

Op elke zich zelf respecterende site vind je middelen om de inhoud van je download te controleren. Hieronder als voorbeeld een bestand van een debian website. Eerst downloaden we de iso en SHA512SUM en SHA512SUM.sign. Om te checken of de sotware te vertrouwen is gebruiken we dit commando:

    gpg --verify SHA512SUMS.sign

Maar wat doe je als je deze foutmelding krijgt?

    gpg: assuming signed data in 'SHA512SUMS'
    gpg: Signature made Fri 11 Jun 2021 12:46:57 AM CEST
    gpg:                using RSA key DF9B9C49EAA9298432589D76DA87E80D6294BE9B
    gpg: Can't check signature: No public key

Om deze download te verifieren heb je de public key nodig. Voor debian kun je die verkrijgen via de debian keyring server:

    gpg --keyserver keyring.debian.org --recv DF9B9C49EAA9298432589D76DA87E80D6294BE9B

Als alles goed gaat krijg je deze melding:

    gpg: key DA87E80D6294BE9B: public key "Debian CD signing key <debian-cd@lists.debian.org>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1

Nu kun je pas verifieren of de sofware vertrouwd is:

    gpg --verify SHA512SUMS.sign

Je krijgt nu een dergelijke melding:

    gpg: assuming signed data in 'SHA512SUMS'
    gpg: Signature made Fri 11 Jun 2021 12:46:57 AM CEST
    gpg:                using RSA key DF9B9C49EAA9298432589D76DA87E80D6294BE9B
    gpg: Good signature from "Debian CD signing key <debian-cd@lists.debian.org>" [unknown]
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: DF9B 9C49 EAA9 2984 3258  9D76 DA87 E80D 6294 BE9B

"good signature" en de datum doen vermoeden dat dit een goede download was.
De fingerprint kun je alleen van de eigenaar krijgen, en het is niet de bedoeling dat elke debian developper die uitwisselt met elke eindgebruiker. :-)

Bij elke download kan een andere signature gebruikt worden. als dat zo is kun je de oude key weer verwijderen:

    gpg --delete-keys DF9B9C49EAA9298432589D76DA87E80D6294BE9B

## 02 maak je eigen private en publieke key

Om bestanden te kunnen versleutelen heb je een key nodig. De meest gebruikte vorm van versleuteling heet a-synchrone versleuteling. Versleutelen doe je met je private key, degene met wie je het bestand deelt heeft het publieke deel nodig.

    gpg --full-gen-key

## 03 pass

Om je wachtwoorden op te slaan kun je gebruik maken van pass. Dit is een programma voor de terminal dat gebruik maakt van gpg versleuteling om je wachtwoorden op te slaan.

    sudo apt install pass

Daarna moet je het programma koppelen aan je secret gpg-key. die vind je met:

    gpg --list-secret-keys --keyid-format LONG

de output ziet er als volgt uit:

    sec   rsa4096/252B4AF5FFCEFE84 2021-07-04 [SC]
          220BEBE7A3900879E8EADFD1252B4AF5FFCEFE84
    uid                 [ultimate] Jack van Lisdonk (Persoonlijke key van Jack) <jack.en.anja@gmail.com>
    ssb   rsa4096/5D48735A4AA80E02 2021-07-04 [E]

Je gpg-id is in dit geval: **252B4AF5FFCEFE84**. Vervolgens kun je pass initieren met:

    pass init "252B4AF5FFCEFE84"

Een nieuw wachtwoord stel je in met:

    pass insert gmail/stauntonel@gmail.com

Geef vervolgens je wachtwoord in.

[Meer beheer](/categories/beheer)
