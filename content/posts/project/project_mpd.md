---
title: "mpd, ncmpcpp, mpc en icecast"
date: 2021-02-14T14:44:45+01:00
draft: false
author:
description: "mpd en ncmpcpp installeren"
categories:
  - project
tags:
  - debian
  - muziek
slug: project-debian-muziek-mpd
toc: true
---

Music playing deamon is een deamon waarmee je complete muziek collecties kunt organiseren, en afspelen met een client, zoals ncmpcpp. Ook kun met een server mpd en icecast je eigen internet radio starten.

<!--more-->

## 01 MPD op desktop

### MPD installeren

Installeer MPD onder een gewone gebruikeren een client, in dit geval ncmpc:

    sudo apt install mpd ncmpcpp xdg-user-dirs
    xdg-user-dirs-update
    mkdir -p /home/stauntonel/Music/mpd/playlists

Van mijn arch git repo kun je het configbestand kopieren naar je homedir

### xdg

Of je kunt met xdg je config file in de .config map zetten. Daarvoor moet je wel even de volgende aktie uitvoeren:

    mkdir /home/stauntonel/.config/mpd
    sudo nano /etc/profile

Op het einde van het bestand voeg je de volgende regel toe:

    export XDG_CONFIG_HOME="$HOME/.config"

### MPD configureren

Kopieer de config-file naar /home/stauntonel/.config/mpd/mpd.conf. Check vooraf nog even de paden in de config. Voorbeeld config:

    music_directory     "~/Music"
    playlist_directory  "~/Music/mpd/playlists"
    db_file             "~/Music/mpd/database"
    log_file            "~/Music/mpd/log"
    pid_file            "~/Music/mpd/pid"
    state_file          "~/Music/mpd/state"
    sticker_file        "~/Music/mpd/sticker.sql"

    input {
            plugin "curl"
    }

    audio_output {
    	type		"pulse"
    	name		"My Pulse Output"
    }

    audio_output {
        type                    "fifo"
        name                    "my_fifo"
        path                    "/tmp/mpd.fifo"
        format                  "44100:16:2"
    }

### MPD automatisch opstarten

Dan kunnen we de mpd service starten met:

    sudo systemctl stop mpd
    sudo systemctl disable mpd
    systemctl --user enable mpd
    systemctl --user start mpd

### ncmpcpp

Met deze client kun je prima music afspelen in de terminal! Maak een map in $XDG_CONFIG_HOME:

    cd $XDG_CONFIG_HOME
    mkdir ncmpcpp
    cd ncmpcpp
    nano config

Geef het bestand de volgende inhoud:

    ncmpcpp_directory = ~/.config/ncmpcpp
    mpd_host = localhost
    mpd_port = 6600
    mpd_music_dir = ~/music
    visualizer_fifo_path = /tmp/mpd.fifo
    visualizer_output_name = "my_fifo"
    visualizer_in_stereo = yes
    visualizer_sync_interval = 30
    visualizer_type = "spectrum"
    visualizer_look = ●▮
    visualizer_color = 41, 83, 119, 155, 185, 215, 209, 203, 197, 161

## 02 Mpd met icecaste op server

### Icecast deamon

Installeer mpd op ubuntu server 16.04 LTS

    apt-get install --no-install-recommends mpd lame flac mpc ncmpcpp icecast2

verander de wachtwoorden in /etc/icecast2/icecast.xml

    <authentication>
          <!-- Sources log in with username 'source' -->
          <source-password>########</source-password>
          <!-- Relays log in with username 'relay' -->
          <relay-password>hackme</relay-password>

          <!-- Admin logs in with the username given below -->
          <admin-user>stauntonel</admin-user>
          <admin-password>########</admin-password>
    </authentication>

Activeer nu de icecast deamon:
nano /etc/default/icecast2:

    ENABLE=true

restart de deamon, en check de status

    systemctl restart icecast2
    systemctl status icecast2

check of de deamon draait onder de icecase2 gebruiker, niet als root!

    ps -aux| grep icecast

### mpd

check of de user stauntonel lid is van de audio groep

    id stauntonel

Als dat niet zo is maak stauntonel lid van group audio

    usermod -a -G audio stauntonel

check of gebruiker mpd lid is van de group audio

    id mpd

Maak een map voor de music collection:

    mkdir -p /home/stauntonel/Music
    chown -R stauntonel:audio /home/stauntonel/Music/
    find /home/stauntonel/Music -type d -exec chmod 770 {} +
    find /home/stauntonel/Music -type f -exec chmod 660 {} +
    chmod 3770 /home/stauntonel/Music

aanpassen mpd instellingen nano /etc/mpd.conf:

    music_directory         "/home/stauntonel/Music"
    bind_to_address         "127.0.0.1"
    #audio_output {
    ##       type            "alsa"
    ##       name            "My ALSA Device"
    ##       device          "hw:0,0"        ## optional
    ##       mixer_type      "hardware"      ## optional
    ##       mixer_device    "default"       ## optional
    ##       mixer_control   "PCM"           ## optional
    ##       mixer_index     "0"             ## optional
    #}
    audio_output {
            type            "shout"
    ##       encoding        "ogg"                   ## optional
            name            "StauntonelRadio"
            host            "localhost"
            port            "8000"
            mount           "/radio"
            password        "########"
            quality         "5.0"
    ##       bitrate         "128"
            format          "44100:16:2"
            protocol        "icecast2"              ## optional
            user            "source"                ## optional
            description     "Radio stauntonel"      ## optional
    ##       url             "http://example.com"    ## optional
    ##       genre           "jazz"                  ## optional
            public          "no"                    ## optional
    ##       timeout         "2"                     ## optional
    ##       mixer_type      "software"              ## optional
    }

### mpc

instellingen:

    mpc help
    mpc random on
    mpc repeat on
    mpc ls | mpd add
    mpc play

### afspelen

met elke player: url http://192.168.178.26:8000/radio

[meer projecten](/categories/project)
