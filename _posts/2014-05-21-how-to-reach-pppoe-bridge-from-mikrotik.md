---
id: 22
title: How to reach PPPoE bridge from Mikrotik
date: 2014-05-21T18:20:28+01:00
author: james
excerpt: |
layout: post
guid: http://blog.dical.org/?p=22
permalink: /how-to-reach-pppoe-bridge-from-mikrotik/
frutiful_posts_template:
  - "3"
categories:
  - Uncategorized
---
When using a Mikrotik router (or any other decent home router) as your PPPoE client, it&#8217;s good to be able to keep access to the ADSL/VDSL modem in-line to allow diagnostics, additional configuration etc. To avoid a situation where the router is essentially double-NATing all the packets going across the WAN link, the ideal setup is a secondary IP address on the router&#8217;s WAN interface, that is handled separately.

Below is a crude drawing of my home setup. The Draytek Vigor 130 is acting as a VDSL modem, bridging the PPPoE connection across to the Mikrotik <span style="color: #333333;">RB2011UAS-2HnD-IN which is &#8216;dialing&#8217; the PPPoE connection back to my ISP. The ISP dishes out an IP address which lands directly on the router, passing through the Draytek. On the LAN side of the router, is a pretty boring DHCP subnet (with DNS setup as mentioned <a href="http://blog.dical.org/2014/05/unblock-us-and-mikrotik-rb2011uas-2hnd-in/">here</a>).</span>

[<img loading="lazy" class="alignnone wp-image-23 size-large" src="https://i0.wp.com/blog.dical.org/wp-content/uploads/2014/05/setup.png?resize=840%2C161&#038;ssl=1" alt="" width="840" height="161" srcset="https://i0.wp.com/blog.dical.org/wp-content/uploads/2014/05/setup.png?resize=1024%2C196&ssl=1 1024w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2014/05/setup.png?resize=300%2C57&ssl=1 300w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2014/05/setup.png?resize=768%2C147&ssl=1 768w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2014/05/setup.png?w=1215&ssl=1 1215w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" />](https://i0.wp.com/blog.dical.org/wp-content/uploads/2014/05/setup.png?ssl=1)

To setup the router/modem to allow access to both (without unplugging the router to get back to the modem) &#8211; you can do the following:

First, add an IP address to the modem:

<pre>(On Draytek, using GUI, added 192.168.2.1 255.255.255.252)</pre>

Now, add the corresponding interface on the Mikrotik (access via SSH, note below is only 2 lines of config):

<pre>/ip address
add address=192.168.2.2/30 comment="To get to VDSL modem" interface=ether1-gateway network=192.168.2.0</pre>

This will add the other end of the /30 network to the ether1-gateway (physical) interface on the Mikrotik router.

Now, all we need to do is tell the router that it&#8217;s OK to NAT on that address, on that interface:

<pre>/ip firewall nat
add action=masquerade chain=srcnat out-interface=ether1-gateway</pre>

If all went to plan, you should be able to ping 192.168.2.1 from your Mikrotik:

<pre>[admin@burnett-home] &gt; ping 192.168.2.1
HOST                                     SIZE TTL TIME  STATUS
192.168.2.1                                56 255 0ms
192.168.2.1                                56 255 0ms
    sent=2 received=2 packet-loss=0% min-rtt=0ms avg-rtt=0ms max-rtt=0ms
</pre>

All done, now you can browse to both the router IP for GUI config, and get to the VDSL modem&#8217;s config page as well.

Help on this one came from the [DD-WRT wiki](http://www.dd-wrt.com/wiki/index.php/Access_To_Modem_Configuration).