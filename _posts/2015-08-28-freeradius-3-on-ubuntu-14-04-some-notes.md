---
id: 49
title: 'FreeRADIUS 3 on Ubuntu 14.04 - some notes'
date: 2015-08-28T02:56:41+01:00
author: james
excerpt: |
layout: post
guid: http://blog.dical.org/?p=49
permalink: /freeradius-3-on-ubuntu-14-04-some-notes/
categories:
  - Uncategorized
---
Ugh. Nothing worse than setting out to install something relatively simple (like FreeRADIUS) only to find the documentation confusing, inconsistent and contradictory. FreeRADIUS has some pretty decent documentation for the actual operation of the software, but getting things going up-front can be a real mission, especially, if, like me, you've never done it before. I'm writing this as I go. My goal is to have a FreeRADIUS server running on my server, which I can use to test RADIUS functionality with some lab routers - Juniper MX480, MX960 and M120s.

<!--end_excerpt-->

First of all, why are you installing FreeRADIUS? What do you stand to gain here? If you already know and you're hitting some glitches with the installation, these notes might help. If you want something a bit more comprehensive written by an expert - try <a href="http://freeradius.org/doc/getting_started.html" target="_blank" rel="noopener noreferrer">here</a>.

As FreeRADIUS comes with a pre-compiled package for my poorly chosen operating system (after many problems with Ubuntu 14.04 I've gone back to Debian 7.8 for nearly everything). That's great news - simply run the lazy person's favourite command:

sudo apt-get install freeradius -y

Now that bit is done, the docs tell you to run the 'radiusd -X' commands. From what I can see, that program doesn't come with the Ubuntu package anymore. Simply replace it with 'freeradius -X' - it starts FreeRADIUS in debug mode, and away you go. Note - you might find that the 'freeradius' service has already started post installation.. Check with:

ps aux | grep freerad

If it's in there, do a quick 'sudo service freeradius stop' and then you can fire it up again in debug (-X) mode.

Due to the way FreeRADIUS is recommended to be installed, the folder '/etc/freeradius' (where the installer assumes you want things put) is owned by root. It might pay to do the rest of the work as root, if you can. Makes things a bit smoother, than having to use sudo all the time.

Add a user in the /etc/freeradius/users file, add a client in the /etc/freeradius/clients.conf and start the service up (I recommend debug). The formatting for adding a user/client _is_ actually documented well. Remember, a **user** is a user on your router/device, a **client** is the configuration knob required on your RADIUS gateway (in this case, also a router). You need a secret phrase between the router and RADIUS server to allow them to talk, as well as a user defined on both sides (the subject of the talk between client and server).