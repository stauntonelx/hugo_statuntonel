---
title: A. Hugo basis
date: 2022-10-09T11:19:30+02:00
draft: false
description: Basis voor een nieuwe website.
categories:
  - web
tags:
  - hugo
  - basis
slug:
  - hugo-basis
toc: true
---

Om een website met hugo te maken kun je kiezen voor een [theme](https://themes.gohugo.io/) op de website.
Of je kunt je website zelf opbouwen zoals we hier gaan doen.

<!--more-->

## 01 basis mappenstructuur

Om een nieuwe website aan te maken gebruiken we het volgende stappenplan.

### hugo help

Hugo heeft een ingebouwde helpfunctie

    hugo --help

Ook kun je voor elk commando extra help krijgen met

    hugo new site --help

Verder is er natuurlijk nog de [online documentatie](https://gohugo.io/documentation/).

### basis website

We gaan nu een nieuwe site starten met een yaml config file:

    hugo new site -f "yaml" ontwikkel01

We hebben nu een nieuwe mappen structuur voor je website.

    tree ontwikkel01

de structuur ziet er als volgt uit:

    ontwikkel01/
    ├── archetypes
    │   └── default.md
    ├── config.yaml
    ├── content
    ├── data
    ├── layouts
    ├── static
    └── themes

Een lege mappenstructuur met slechts 2 bestanden:

config.yaml
: configuratiebestand voor de nieuwe website

archetypes/default.md
: configuratie bestand voor nieuwe pagina's

### basis thema

Hugo kent ook een commando voor een nieuw thema te maken,

    cd ontwikkel01
    hugo new theme MijnThema

Even checken of het thema is toegevoegd:

    tree themes/MijnThema

Als het goed is heb je nu deze mappenstructuur in de "themes" map:

    └── MijnThema
    ├── archetypes
    │   └── default.md
    ├── layouts
    │   ├── 404.html
    │   ├── _default
    │   │   ├── baseof.html
    │   │   ├── list.html
    │   │   └── single.html
    │   ├── index.html
    │   └── partials
    │       ├── footer.html
    │       ├── header.html
    │       └── head.html
    ├── LICENSE
    ├── static
    │   ├── css
    │   └── js
    └── theme.toml

### opruimen bestanden

Omdat we niet met een thema willen werken maar wel de basis van een thema willen overnemen voeren we nu de volgende commando's uit:

    mv themes/MijnThema/layouts .
    mv themes/MijnThema/static .
    rm -rf {themes,resources,data}

Let op! dat je in de hoofdmap van je website staat. (ontwikkel01)
Je mappenstructuur ziet er nu als vogt uit:

    ├── archetypes
    │   └── default.md
    ├── config.yaml
    ├── content
    ├── layouts
    │   ├── 404.html
    │   ├── _default
    │   │   ├── baseof.html
    │   │   ├── list.html
    │   │   └── single.html
    │   ├── index.html
    │   └── partials
    │       ├── footer.html
    │       ├── header.html
    │       └── head.html
    └── static
        ├── css
        └── js

## 02 templates

### basis overzicht

Hieronder een overzicht van de meest belangrijke templates.

baseof.html
: Het geraamte van de website

index.html
: landingspagina voor de website

## 03 baseof.html

Hugo is een programma wat gebruik maakt van de programmeertaal GO.
Go statements staan tussen {{ }}.

    !DOCTYPE html>
    <html>
        {{- partial "head.html" . -}}
        <body>
            {{- partial "header.html" . -}}
            <div id="content">
            {{- block "main" . }}{{- end }}
            </div>
            {{- partial "footer.html" . -}}
        </body>
    </html>

### partials

In de baseof.html worden een aantal partials aangeroepen.
Je vind ze in de map *layouts/partials*.
Voor nu geven we de aangeroepen partials een minimale vulling om te zien hoe HUGO hier mee om gaat.

*head.html*

    <head>
        <title>{{ .Title }} | {{ .Site.Title }}</title>
        <meta http-equiv="content-type" content="text/html;charset=utf-8" />
        <meta name="generator" content="Geany 1.37.1" />
    </head>

*header.html*

    <header>
        <p>header</p>
        <hr>
    </header>

*footer.html*

    <footer>
        <hr>
        <p>footer</p>
    </footer>

## 04 index.html

Iedere website heeft een landingspagina nodig, de zogenaamde *index.html*

    {{ define "main" }}
        <h1>Hello world</h1>
    {{ end }}

Nu hebben we de basis voor een simpele website

## 05 hello world

Gefeliciteerd je hebt nu de basis voor je nieuwe website weggezet!
Je kunt deze oproepen met:

    hugo server

Daarna kun je de pagina oproepen in de browser met **localhost:1313**
