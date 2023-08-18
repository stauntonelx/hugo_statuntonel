---
title: "arch openbox"
date: 2021-02-14T20:49:19+01:00
draft: false
author:
categories:
  - desktop
description: "Installeer Openbox na Arch installatie met xorg"
tags:
  - arch
slug: desktop-arch-openbox
toc: true
---

Installeer openbox na basis installatie arch met xorg.

<!--more-->

## 01 Voorbereidingen in de terminal

### Xinit

Als gebruiker dus niet als root:

    sudo pacman -S xorg-xinit
    cp /etc/X11/xinit/xinitrc ~/.xinitrc
    nano ~/.xinitrc

Om openbox te openen met startx, voeg de volgende regel toe in het bestand

    exec openbox-session

### Configuratiebestanden

Configuratiebestanden klaarzetten:

    cp -R /etc/xdg/openbox ~/.config/

De configuratiebestanden zijn:

  * autostart, programma's die moeten opstarten met openbox
  * rc.xml, voor de keybindings
  * menu.xml, voor het openbox menu

### Gebruikersmappen

De standaard gebuikersmappen in je home-partitie aanmaken:

    sudo pacman -S pacman -S xdg-user-dirs
    xdg-user-dirs-update

### Achtergrond instellen

Zonder achtergrond is openbox een beetje saai:

    mkdir -p ~/Pictures/wallpapers/
    cd ~/Pictures/wallpapers/
    sudo pacman -S feh
    wget http://www.desktopmachine.com/pics/Ford_Mustangxx_2014_01_1920x1080.jpg
    feh --bg-scale ~/Pictures/wallpapers/Ford_Mustangxx_2014_01_1920x1080.jpg

Om de achtergrond vast te houden

    nano ~/.config/openbox/autostart

Voeg de volgende regel toe

    ~/.fehbg &

### Dmenu

Omdat het menu van openbox niet echt dynamisch is, installeren we ook Dmenu

    pacman -S dmenu
    cp ~/.config/openbox/rc.xml ~/.config/openbox/rc_sav.xml
    nano ~/.config/openbox/rc.xml

daarna een keybinding aanmaken

```xml
<keybind key="W-r">
  <action name="Execute">
    <startupnotify>
      <enabled>true</enabled>
      <name>dmenu</name>
    </startupnotify>
    <command>dmenu_run</command>
  </action>
</keybind>
```

Nu kun je met de window -R elk geinstalleerd programma openen.

### Openbox menu

Pas het openbox menu aan, hier een simpel voorbeeld:

```xml
<openbox_menu xmlns="http://openbox.org/3.4/menu">
    <menu id="system-menu" label="System">
        <item label="Openbox menu">
            <action name="Execute">
                <execute>obmenu</execute>
            </action>
        </item>
        <item label="Openbox Configuration Manager">
            <action name="Execute">
                <command>obconf</command>
                <startupnotify>
                    <enabled>yes</enabled>
                </startupnotify>
            </action>
        </item>
        <separator/>
        <item label="Reconfigure Openbox">
            <action name="Reconfigure"/>
        </item>
    </menu>
    <menu id="root-menu" label="Openbox 3">
        <item label="Terminal">
            <action name="Execute">
                <execute>termite</execute>
            </action>
        </item>
        <item label="Firefox">
            <action name="Execute">
                <execute>firefox</execute>
            </action>
        </item>
        <menu id="system-menu"/>
        <separator/>
        <item label="Exit">
            <action name="Execute">
                <execute>systemctl poweroff</execute>
            </action>
        </item>
        <item label="Log Out">
            <action name="Exit">
                <prompt>yes</prompt>
            </action>
        </item>
    </menu>
</openbox_menu>
```

### OBmenu

Om het openbox menu aan te passen naar je eigen wensen kun je gebruik maken van obmenu.

    sudo pacman -S obmenu

## 02 xorg

### Gtk-theme

Voor het instellen van themes, icons, lettertype etc. installeer een aantal tools:

    sudo pacman -S lxappearance obconf numix-gtk-theme faenza-icon-theme \
    gtk-engine-murrine

### Compositing

Openbox heeft geen eigen compositer dus die moeten we zelf installeren.

    sudo pacman -S compton

Daarna moeten we er voor zorgen dat compton start als we openbox starten.

    nano ~/.config/openbox/autostart

En voeg de volgende regel toe:

    compton -b &

### Gtk3 settings

Met deze settings los je problemen op met gtk3.

    nano ~/.config/gtk-3.0/gtk.css

Zorg dat dit bestand er alsvolgt uitziet:

```css
.window-frame, .window-frame:backdrop {
 box-shadow: 0 0 0 black;
 border-style: none;
 margin: 0;
 border-radius: 0;
}

.titlebar {
 border-radius: 0;
}

.window-frame.csd.popup {
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2), 0 0 0 1px rgba(0, 0, 0, 0.13);
}

.header-bar {
  background-image: none;
  background-color: #ededed;
  box-shadow: none;
}
/* You may want to use this if you don't like the double title.
GtkLabel.title {
    opacity: 0;
}*/ 
```

Verder zorg je ervoor dat er geen buttons in de header komen met:

    nano ~/.config/gtk-3.0/settings.ini

Pas deze regel aan:

    gtk-decoration-layout=menu:

[Meer desktop](/categories/desktop)
