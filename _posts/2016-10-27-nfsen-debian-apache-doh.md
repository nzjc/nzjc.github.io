---
id: 142
title: "nfsen + debian + apache = d'oh"
date: 2016-10-27T14:05:01+01:00
author: james
layout: post
guid: https://normally.online/?p=142
permalink: /nfsen-debian-apache-doh/
categories:
  - Uncategorized
---
I was re-doing one of my lab monitoring tools, a VM that hosted too many sparse and poorly maintained pieces of software. Now re-homing each bit onto its own VM (partially for sanity) - I ended up re-installing the excellent NFSen (a netflow monitoring tool/frontend for nfdump).

<!--end_excerpt-->

The software includes a directory named 'icons' in the web root, which doesn't seem insane to me. What is insane, however, is Apache's decision (by default!) to include an alias for a folder named 'icons' in the root. That means that without knowing it, the NFSen icons folder was being redirected to /usr/share/apache2/.../ whatever. That caused a headache.

To find this out, I ran:  
`cd /etc/apache2<br />
grep -iR /usr/share *`

This told me about the dang alias file, /etc/apache2/mods-available/alias.conf

I went into that file, commented out this dumb default, reset apache and now it's away laughing.