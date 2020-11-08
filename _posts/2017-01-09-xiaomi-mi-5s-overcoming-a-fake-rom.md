---
id: 156
title: 'Xiaomi Mi 5s - Overcoming a fake ROM'
date: 2017-01-09T05:08:16+00:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=156
permalink: /xiaomi-mi-5s-overcoming-a-fake-rom/
categories:
  - Uncategorized
---
&nbsp;

I bought a fancy pants new Xiaomi phone. My Samsung S5 was getting a bit long in the tooth. I wanted another flagship, but Samsung's ROMs have been bloating up recently and while I'm a custom ROM fanboy, I was getting sick of Cyanogenmod and the occasional app not working with an unlocked bootloader, custom ROM etc.

<!--end_excerpt-->

So I got a Xiaomi Mi 5S for about $400 NZD from Banggood. The phone and case arrived 7 days after I ordered, so far so good. What I didn't know in advance was Banggood (and other vendors) sneakily put their own ROM on the phone which is rambo jambo'd with Malware, Adware and can't receive OTA (over the air) software updates - in short, it's a shit sandwich.

The [MIUI forums](http://en.miui.com/forum.php) are a great place to get help, but if you have a Banggood Mi 5s phone with an 8.x.x.x fake ROM - you can probably follow these steps.

  1. First up, confirm you are on a fake ROM. [This page](http://en.miui.com/thread-309122-1-1.html) is a good way to do so. I had 8.1.1.18(OxxxxCNX) and it looked bad news. I couldn't do a software update or run Pokemon Go, so I decided I was affected and moved on.
  2. Unlock your Boot Loader. What does this mean? Well, it's a setting on an Android phone that lets you install custom software. In our case, we're actually trying to do the opposite, by rolling this dodgy fake ROM back to the official MIUI release. Nevertheless, we need to [unlock our BL](http://www.miui.com/unlock/). Xiaomi makes us do this via logging in to their website and requesting an unlock on our Mi account. [Simply create a Mi](http://www.miui.com/unlock/) account and request an unlock. It might take a few days - mine took approx. 72 hours to have a verification SMS.
  3. Once we have the confirmation that the Mi account is enabled for unlocking, we need to download some software (unfortunately you need a Windows PC for this - although I did succeed using a VMWare Fusion VM on OSX). This software is the Unlocking app - we need to log in to the Mi account on the phone and inside this programme simultaneously - otherwise it will stall on 50% in the unlocking process. So - [click here](http://api.bbs.miui.com/url/MiFlash) to get the latest Mi Flash.
  4. Open Mi Flash programme, turn your phone on in Recovery mode (Hold volume Up/Pwr button at the same time, from when the phone is off). Log in on the application and select Unlock.. This should work smoothly.
  5. Now, we have unlocked the bootloader, we should be able to use the Update application on the phone itself (usually in a folder called Tools). Best bet is to go to the [official source](http://en.miui.com/download.html) and grab a Global Rom (assuming you don't want a Chinese one) - over here. Once you have the rom, put it on your phone's internal storage in a folder called downloaded_rom (create one if it doesn't exist), open the aforementioned Update app and select the .zip file you just uploaded. It will take about 45 mins to work.

Mean buzz - you have a official Xiaomi phone/ROM - not some dodgy crippleware. Instead of asking questions here, I recommend heading to the MIUI forums, already linked. Try and only use official software from Xiaomi/MIUI.. Not everyone out there is friendly.

&nbsp;