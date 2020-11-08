---
id: 193
title: Juniper to Fortinet ISIS configuration
date: 2017-09-04T16:52:24+01:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=193
permalink: /juniper-to-fortinet-isis-configuration/
categories:
  - Uncategorized
---
Hoo boy. I have been trying to configure a small mesh network for a fault-resilient office setup. In my network, I have a 'square' setup, two VMX routers, two Fortigate virtual firewall appliances, all running on top of ESXi 6.5 (two physical hypervisors). It looks like this:

<!--end_excerpt-->

<img loading="lazy" class="alignnone size-large wp-image-196" src="https://i2.wp.com/blog.dical.org/wp-content/uploads/2017/09/lab-vmx-isis.png?resize=840%2C453&#038;ssl=1" alt="" width="840" height="453" srcset="https://i2.wp.com/blog.dical.org/wp-content/uploads/2017/09/lab-vmx-isis.png?resize=1024%2C552&ssl=1 1024w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2017/09/lab-vmx-isis.png?resize=300%2C162&ssl=1 300w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2017/09/lab-vmx-isis.png?resize=768%2C414&ssl=1 768w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2017/09/lab-vmx-isis.png?w=1344&ssl=1 1344w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

Anyway. In order to redistribute the default route(s) received from the upstreams, I wanted to use iBGP inside the 'square' of devices.. iBGP relies on an IGP, so I chose the coolest one available, ISIS.

This is a very simple setup, but there was no way I could get an adjacency to form between the router and firewall (green to black in the diagram). I tried 100 things (changing hello intervals ([pointless](http://packetlife.net/blog/2008/may/14/hello-timer-behaviors/)!), LSP generation times, MTU, MTU, MTU and several other desperate things like disabling hello-padding, enabling and disabling 'adjacency checking' on the Forti-devices).. Nothing.

Eventually, I enabled trace-options on the Junos side of things - I could see my adjacencies with the Forti-devices stuck in the 'Initializing' phase, implying the three-way-handshake was busted.. The traceoptions showed some guff, but nothing that pointed to an easily solvable problem (i.e. not MTU)..

Finally, using the debug features of the Fortigate box, I found:

<pre class="lang:default decode:true">id=20301 logdesc="Routing log" msg="IS-IS: PDU[RECV]: P2P-Hello IS-

Neighbor(port2-0192.1681.0020) IPv6 protocols supported mismatch</pre>

Bearing in mind, there isn't a single bit of IPv6 config on any of these devices (yet, it's going to be fully dual stack, don't worry!) - so what was up.. Turns out, the Fortigate devices were a bit sensitive, and needed the following knob in my Juniper ISIS config:

<pre class="lang:default decode:true ">james@vmx-2&gt; show configuration protocols isis | display set
set protocols isis no-ipv6-routing
</pre>

All of a sudden.. My ISIS adjacencies are up and solid.

Hopefully this will be useful to some sucker in future who chooses to use ISIS in their corporate network 🙂

&nbsp;