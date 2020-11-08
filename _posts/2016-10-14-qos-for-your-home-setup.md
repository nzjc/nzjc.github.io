---
id: 137
title: QoS for your Linux Plex box
date: 2016-10-14T10:21:17+01:00
author: james
excerpt: |
layout: post
guid: http://blog.dical.org/?p=137
permalink: /qos-for-your-home-setup/
categories:
  - Uncategorized
---
<figure id="attachment_138" aria-describedby="caption-attachment-138" style="width: 596px" class="wp-caption aligncenter">[<img loading="lazy" class=" wp-image-138" src="https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/10/Screen-Shot-2016-10-10-at-11.34.33-pm.png?resize=596%2C449&#038;ssl=1" alt="FireQos in action" width="596" height="449" srcset="https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/10/Screen-Shot-2016-10-10-at-11.34.33-pm.png?resize=300%2C226&ssl=1 300w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/10/Screen-Shot-2016-10-10-at-11.34.33-pm.png?resize=768%2C578&ssl=1 768w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/10/Screen-Shot-2016-10-10-at-11.34.33-pm.png?resize=1024%2C771&ssl=1 1024w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/10/Screen-Shot-2016-10-10-at-11.34.33-pm.png?w=1068&ssl=1 1068w" sizes="(max-width: 596px) 85vw, 596px" data-recalc-dims="1" />](https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/10/Screen-Shot-2016-10-10-at-11.34.33-pm.png?ssl=1)<figcaption id="caption-attachment-138" class="wp-caption-text">FireQos in action</figcaption></figure> 

When [Jim Salter](https://twitter.com/jrssnet) posted about FireQos the other day, it made me take note. FireQos is a unix&#8217;y firewall &#8216;for humans&#8217;. In my day job, QoS is a complex and multi-faceted thing, requiring tonnes of design, thought and understanding to implement correctly (not to mention hardware). It has dramatic effects on network traffic when set up correctly, but that usually means end-to-end config across a domain, so marking at one end of the network translates to actions all the way through. That&#8217;s a bit much for home.

I was interested, as I had a problem. Behind my TV I have an Intel NUC, with a little i3 processor and 802.11n wifi. I use it to torrent things, run a Plex server and be a multi-purpose Linux machine for my own needs the rest of the time. (OwnCloud is still [running on the Raspberry Pi 3](http://blog.dical.org/2016/04/owncloud-9-0-1-on-raspberry-pi-3-step-by-step/), mind you). When I was pulling down a delicious new Debian image at 12MB/s, and trying to watch something on Plex (via the PS4), things got a bit choppy. Try to VNC into the box from my laptop to throttle the torrent was always annoying, it could take minutes for the screen to refresh if a very hearty download was going on. Like most nerds, the slightest delay caused by my own setup was slowly tearing me apart.

This is where FireQos comes in. With a very simple install and a couple of minutes of settings out of the way, the performance improved dramatically. All I did was prioritise the traffic for Plex, SSH, VNC and browsing over torrents/anything else &#8211; and like magic, everything works smoothly altogether &#8211; with no throttling on the torrent client.

Remember before where I said QoS really needs to be end-to-end in the network to make a difference? In this case, not true. By simply tweaking the Linux handling of packets, things have gotten much better with the rest of the network unaware anything is happening. Obviously, this would improve if I had a router that was also participating in the fun, but I don&#8217;t.. [Yet](http://arstechnica.com/gadgets/2016/04/the-ars-guide-to-building-a-linux-router-from-scratch/). At the moment, if another device tries to use the network when a full torrent storm is going on, it&#8217;s toast.

Anyhow, check out the FireQos tutorial [here](https://github.com/firehol/firehol/wiki/FireQOS-Tutorial), and give it a crack yourself. There&#8217;s basically no risk, go nuts.

Here&#8217;s my fireqos.conf file, so you can copypasta it if you like.

<pre class="prettyprint">DEVICE=wlan0
INPUT_SPEED=120000kbit
OUTPUT_SPEED=120000kbit

interface $DEVICE world-in input rate $INPUT_SPEED
interface $DEVICE world-out output rate $OUTPUT_SPEED

interface $DEVICE world-in input rate $INPUT_SPEED
   class interactive commit 20%
 	match udp port 53         
    	match tcp port 22             
    	match icmp                    
    	match tcp sports 5222,5228    
    	match tcp sports 5223

    class plex commit 50%
    	match udp port 1900
    	match tcp port 3005
    	match udp port 5353
    	match tcp port 8324
    	match udp port 32410
    	match udp port 32412
    	match udp port 32413
    	match udp port 32414
    	match tcp port 32469

    class vnc commit 5000kbit
    	match tcp port 5901

   class surfing commit 20%
	match tcp sports 0:1023

   class synacks            
      match tcp syn                    
      match tcp ack                 

   class default

   class torrents
      match dports 6881:6999
      match dport 51414 prio 1 

interface $DEVICE world-out output rate $OUTPUT_SPEED
   class interactive commit 20%
      match udp port 53             
      match tcp port 22             
      match icmp                    
      match tcp dports 5222,5228    
      match tcp dports 5223         

    class plex commit 50%
      match udp port 1900
      match tcp port 3005
      match udp port 5353
      match tcp port 8324
      match udp port 32410
      match udp port 32412
      match udp port 32413
      match udp port 32414
      match tcp port 32469

    class vnc commit 5000kbit
      match tcp port 5901

   class surfing commit 20%
      match tcp dports 0:1023

   class synacks                       
      match tcp syn                    
      match tcp ack                   

   class default

   class torrents
      match dports 6881:6999        
      match dport 51414 prio 1
</pre>