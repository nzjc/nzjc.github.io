---
id: 142
title: 'nfsen + debian + apache = d&#8217;oh'
date: 2016-10-27T14:05:01+01:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=142
permalink: /nfsen-debian-apache-doh/
categories:
  - Uncategorized
---
I was re-doing one of my lab monitoring tools, a VM that hosted too many sparse and poorly maintained pieces of software. Now re-homing each bit onto its own VM (partially for sanity) &#8211; I ended up re-installing the excellent NFSen (a netflow monitoring tool/frontend for nfdump).

The software includes a directory named &#8216;icons&#8217; in the web root, which doesn&#8217;t seem insane to me. What is insane, however, is Apache&#8217;s decision (by default!) to include an alias for a folder named &#8216;icons&#8217; in the root. That means that without knowing it, the NFSen icons folder was being redirected to /usr/share/apache2/&#8230;/ whatever. That caused a headache.

To find this out, I ran:  
`cd /etc/apache2<br />
grep -iR /usr/share *`

This told me about the dang alias file, /etc/apache2/mods-available/alias.conf

I went into that file, commented out this dumb default, reset apache and now it&#8217;s away laughing.