---
id: 9
title: Unblock-US and Mikrotik RB2011UAS-2HnD-IN
date: 2014-05-21T00:04:38+01:00
author: james
excerpt: |
layout: post
guid: http://blog.dical.org/?p=9
permalink: /unblock-us-and-mikrotik-rb2011uas-2hnd-in/
frutiful_posts_template:
  - "3"
categories:
  - Uncategorized
---
If you live somewhere out of the way, like New Zealand, sometimes it pays to use a DNS-proxying service like Unblock-US. Reasons for this are circumventing some draconian geo-blocking rules, but I won't go into that here.

<!--end_excerpt-->

Anyway - if you want to setup your Mikrotik router to use DNS addresses other than your ISPs provided ones (and make use of the DNS cache offered by the router) - follow these steps:

First, SSH into your router (something like admin@routerIP). I use Putty to do this in Windows. Then enter the following lines:

<pre>/ip dns
set allow-remote-requests=yes servers=111.118.175.56,118.127.33.48,208.122.23.23
/ip dns static
add address=192.168.88.1 name=router
</pre>

That adds the 3 Unblock-US DNS addresses they specify to be the router's DNS servers. You can check it worked by the following command:

<pre>/ip dns print
</pre>

Now, the DNS is set, but nothing on your home LAN is going to be interested until you either statically point each device to use the DNS address of your router (192.168.88.1 by default), or tell the router's DHCP server where to get its DNS info from. The latter is the most elegant option. Do it by:

<pre>/ip dhcp-server
add address-pool=dhcp disabled=no interface=bridge-local lease-time=10m name=default
/ip dhcp-server network
add address=192.168.88.0/24 comment="default configuration" dns-server=192.168.88.1 gateway=192.168.88.1 \
    netmask=24
</pre>

Here, we go into the DHCP server config and set up a DHCP pool on the local bridged interfaces (the locally connected devices, including wifi hosts). Then, we tell the DHCP server to use the router's IP as its DNS source. Quite often this is already set by default. 

Now, you can browse around a bit, using the Unblock-US DNS servers to get to where you want to go. You can check on the DNS Cache by entering:

<pre>/ip dns cache print
</pre>

It should have a few hundred entries after a minute or so of cruising around the internet.

The final (and crucial) step, is to disable 'Peer DNS' on your WAN interface. I did this by disabling the option on my pppoe-out1 interface

<pre>/interface pppoe-client 
use-peer-dns=no
</pre>

Some info from [here](http://www.mikrotik.com/testdocs/ros/2.9/ip/dnscache.php).