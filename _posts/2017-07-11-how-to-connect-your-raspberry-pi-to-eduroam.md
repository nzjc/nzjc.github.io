---
id: 185
title: How to connect your Raspberry Pi to eduroam
date: 2017-07-11T09:10:35+01:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=185
permalink: /how-to-connect-your-raspberry-pi-to-eduroam/
categories:
  - Uncategorized
---
> Note - I took much of the code snippet here from '[Sruc](https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=86253)' on the RPI forums, but wanted to post a clear method that I know works. Cheers Sruc!

The eduroam network (for universities, researchers and highschools around the world) is a great thing. One login lets you connect to wifi access points all over the place, as long as you're enrolled in or working for a participating organisation.

<!--end_excerpt-->

One thing that bugged me out of the box with the Raspberry Pi (in my case, a Raspberry Pi 3 running Pixel) - was the Enterprise WPA wifi not working out of the box.

Follow these simple steps to get it working:

  * Open a Terminal from your Pi's gui (or just use the shell if you don't have a gui!)
  * Open up the wpa_supplicant.conf file:

<pre class="lang:default decode:true ">sudo nano /etc/wpa_supplicant/wpa_supplicant.conf</pre>

  * Paste in the following, changing the bits you normally use to log in to eduroam (your university/whatever email and password is normally what you use for authentication)

(Add this snippet below what's already in the file, **change the 'identity' and 'password' fields!**)

<pre class="lang:default decode:true">network={
        ssid="eduroam"
        scan_ssid=1
        key_mgmt=WPA-EAP
        eap=PEAP
        identity="yourDetails@yourSchool.eu"
        password="yourpassword"
        phase1="peaplabel=0"
        phase2="auth=MSCHAPV2"
}</pre>

  * Save and exit the editor (in nano that's CTRL-O, Enter, CTRL-X)
  * Now we need to tell the Pi to reload the file, again, in the Terminal or shell

<pre class="lang:default decode:true">sudo wpa_supplicant -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf</pre>

  * I find a reboot here is necessary, so flip the Pi and wait for it to boot. When it returns, you should be connected to eduroam (as long as your Pi can see the eduroam SSID!)

Note - I am not sure if this will work for all instances of eduroam, as some Universities etc handle authentication differently - please check your organisation's help pages or get in touch with them first - they usually have a guide.