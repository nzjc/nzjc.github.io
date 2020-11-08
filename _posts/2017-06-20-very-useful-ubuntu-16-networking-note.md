---
id: 178
title: Very useful Ubuntu 16 Networking Note
date: 2017-06-20T10:20:46+01:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=178
permalink: /very-useful-ubuntu-16-networking-note/
categories:
  - Uncategorized
---
I hate when things change for no good reason. This week, it&#8217;s the interface naming of ethernet on Ubuntu 16. No more does it default to &#8216;eth0&#8217;.. It uses some other &#8216;ens&#8217; style.. Garbage!

First up, find your ethernet interfaces (this VM has 1 interface to start):

<pre class="lang:default decode:true">dmesg | grep -i eth
james@GREG:~$ dmesg | grep -i eth
[ 1.479809] vmxnet3 0000:03:00.0 ens33: NIC Link is Up 10000 Mbps</pre>

Bah, looks gross!

Fix it by editing your grub config:

<pre class="lang:default decode:true ">sudo nano /etc/default/grub</pre>

Change the line <span class="lang:default decode:true crayon-inline ">GRUB_CMDLINE_LINUX=&#8221;&#8221;</span>

to  <span class="lang:default decode:true crayon-inline">GRUB_CMDLINE_LINUX=&#8221;net.ifnames=0 biosdevname=0&#8243;</span>

Regenerate your grub file:

<pre class="lang:default decode:true">sudo grub-mkconfig -o /boot/grub/grub.cfg</pre>

Edit your /etc/network/interfaces file, change the names to eth0, eth1 etc

<pre class="lang:default decode:true"># The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
</pre>

Reboot, and voila.

If you add a new interface, it will come on as ethX, following the already provisioned interfaces.

Now it looks better (I added a new 10G interface, and it comes in as eth1)

<pre class="lang:default decode:true">james@GREG:~$ dmesg | grep -i eth
[    1.479809] vmxnet3 0000:03:00.0 eth0: NIC Link is Up 10000 Mbps
[    2.498115] vmxnet3 0000:0b:00.0 eth1: NIC Link is Up 10000 Mbps
</pre>

Awww yeeeeah