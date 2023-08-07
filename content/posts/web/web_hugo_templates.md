---
weight: 103
title: "B. Hugo hello world"
date: 2022-10-11T22:49:34+02:00
draft: true
description: rondje langs diverse templates van je hugo website.
categories:
  - web
tags:
  - hugo
  - templates
slug:
  - hugo-templates
toc: true
---

Hugo maakt gebruik van diverste templates. De belangrijkste komen hier aan bod.

<!--more-->

## 01 list.html

Om een idee te krijgen hoe je de list pagina kunt oproepen geef je die de volgende inhoud:

    {{ define "main" }}
      <p>list page</p>
    {{ end }}

## 02 single.html

Dat doen we ook voor single pagina

    {{ define "main" }}
      <p>single page</p>
        {{ .Content}}
    {{ end }}

## 03 eerste blogpost

Nu maken we onze eerste blogpost met:

    hugo new blog/hello_world.md

Daarna kunnen we met markdown onze eerste post maken:

    nano content/blog/hello_world.md

Om te testen voeren we onderaan de volgende inhoud toe:

    ## Mijn eerste Post!

    Hello world !

Vergeet niet om je blog vrij te geven voor je website:

    draft: false

single page roep je aan met:
: [http://localhost:1313/blog/hello_world/](http://localhost:1313/blog/hello_world/)

Een voorbeeld van de listpage:
: [http://localhost:1313/blog/](http://localhost:1313/blog/)
