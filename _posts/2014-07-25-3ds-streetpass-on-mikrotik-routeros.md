---
id: 32
title: 3DS Streetpass on Mikrotik RouterOS
date: 2014-07-25T00:06:40+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=32
permalink: /3ds-streetpass-on-mikrotik-routeros/
frutiful_posts_template:
  - "3"
categories:
  - Uncategorized
---
Quick and hacky - that's the way you have to Streetpass in New Zealand, as you get so few legit matches out and about.

To setup your own home Streetpass service is easy.

<!--end_excerpt-->

  * Setup a new SSID on your router, as a virtual ap - call it attwifi
  * Dupe the MAC address on the new virtual AP to 4E:53:50:4F:4F:46
  * Stick that SSID in your local bridge and setup some security
  * Join your 3DS to the attwifi SSID and away you go

Simple as that!