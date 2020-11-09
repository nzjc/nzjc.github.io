---
id: 146
title: Source Specific Multicast with iperf
date: 2016-11-07T22:07:34+00:00
author: james
layout: post
guid: https://normally.online/?p=146
permalink: /source-specific-multicast-with-iperf/
categories:
  - Uncategorized
---
<img loading="lazy" class="aligncenter wp-image-151 size-large" src="/wp-content/uploads/2016/11/Screen-Shot-2016-11-07-at-10.13.53-pm.png?resize=840%2C361&#038;ssl=1" alt="oooh iperf ssm" width="840" height="361" srcset="/wp-content/uploads/2016/11/Screen-Shot-2016-11-07-at-10.13.53-pm.png?resize=1024%2C441&ssl=1 1024w, /wp-content/uploads/2016/11/Screen-Shot-2016-11-07-at-10.13.53-pm.png?resize=300%2C129&ssl=1 300w, /wp-content/uploads/2016/11/Screen-Shot-2016-11-07-at-10.13.53-pm.png?resize=768%2C331&ssl=1 768w, /wp-content/uploads/2016/11/Screen-Shot-2016-11-07-at-10.13.53-pm.png?w=1254&ssl=1 1254w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" />

As part of my lab at work, I need to have lots of traffic flying around all over the place. Unicast IPv4 and IPv6, of course - but also traffic in L3VPNs and multicast traffic. Multicast is a big part of the day-to-day traffic load in my production network, so it's important to be there in the lab too.

<!--end_excerpt-->

I've used a variety of tools to generate multicast traffic in the past, more often than not the excellent [OMPing](https://github.com/jfriesse/omping). This time, however, I wanted to really chuck some volume around, to make my stats look nice and to really show up on [NFSen](https://normally.online/2016/10/27/nfsen-debian-apache-doh/). iperf is the natural choice for generating lots of traffic in a hurry, I'm using iperf3 to throw about 20Gbit/s around the lab core, but for some reason the developers have removed multicast functionality from the new and improved iperf3.

**ASM** (or any-source multicast) is (more) traditional multicast. It relies on interested parties joining a group (a multicast address) where any sender can be considered the source (*,G). It works well when your environment is well set up, with a rendezvous point and protocols such as PIM-SM (and MSDP when you wanna go multi-domain). Iperf handles this pretty easily, simply set up a server to 'listen' on one end, and a client to send on the other. This can be achieved by having two hosts, connected via a router which acts as the PIM-SM rendezvous point and has IGMPv2 on the interfaces, so the listener can tell it's router that it's keen on having a listen.

On the listener:  
`iperf -s -B 233.255.1.3 -u -f m -i 1`

On the sender:  
`iperf -c 233.255.1.3 -u -b 100m -f m -i 1 -t -1 -T 32`

On the listener, we're telling iperf to be a server (-s), listening to an ASM multicast group (-B), using UDP (-u), formatting the output in megabit/s (-f) and telling us what's up every 1 second (-i).

On the sender, we're telling iperf to be a client (-c), using UDP, sending a 100Mbit/s stream (-b), sending forever (-t) and setting the multicast TTL to 32 (-T) - overkill, but hey.

This is a quick and easy way to check a basic ASM setup. I use it to confirm my multi-domain lab setup is working with multicast as I would expect and to generate lots of traffic to amuse myself.

**  
Getting source specific**

Source specific multicast is a bit cooler than ASM, because it should work for more people, more easily. If you have a single source (like a TV encoder, or some kind of unique data source) that you want plenty of receivers to see, and you have a nice way of telling them about your source address (like a higher-layer application - or in our case, manual config) - then SSM is the protocol for you.

To make SSM work, you really only need IGMPv3 in your network. Most \*nix OS's and even Windows supports IGMPv3 - usually by default. To check on your \*nix host, you can run:  
`cat /proc/sys/net/ipv4/conf/eth0/force_igmp_version`

If that returns a 0, and you're on a modern-ish OS, you're good to go. You can force your kernel to use IGMPv3 per interface, with:  
`echo "3" > /proc/sys/net/ipv4/conf/eth0/force_igmp_version`

While most OS's support IGMPv3 out of the box, plenty of network administrators in your friendly local LAN have probably forgotten to turn it on, or have left it with IGMPv2 and called it a day. No good.

Assuming you're all good with IGMPv3, you then need to have an application which can listen not only for a particular multicast group address, but also a specific source. iperf, by default, doesn't support this. Lucky for us, then, noushi has written an extension to iperf that allows for a couple of new flags, to set a specific source and interface. You can grab it <a href="https://github.com/noushi/iperf-ssm" target="_blank" rel="noopener noreferrer">here</a>.

If you already had iperf installed, remove it. In Debian/Ubuntu, that would be:  
`sudo apt-get autoremove iperf -y`

Unzip the file from git, then we compile:  
`./configure<br />
make<br />
sudo make install`

Now we can run a client to send multicast as before (but this time, sending to the 232.0.0.0/8 SSM range):  
`iperf -c 232.1.0.55 -u -b 10m -f m -i 1 -t -1 -T 32 -p 5002`

I'm running it on a different port (-p) from default, because I want SSM and ASM at the same time.

For the listener, we need to tell our newly improved iperf to listen for a particular source (the regular old IP address of our sender):  
`iperf -s -B 232.1.0.55 -u -f m -i 1 -p 5002 -O 192.168.66.22 -X eth0.101`

The two new flags are there, along with our SSM multicast group (and new port). The -O flag sets the SSM source and -X tells iperf to use a particular interface. I'm not sure -X is new, but I've never used it before - so let's say it is.

If it's working, we'll see a 10Mbit/s SSM stream turn up on the listener.