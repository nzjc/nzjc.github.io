---
id: 18
title: 'Mikrotik Firewall - For Home Users'
date: 2014-05-21T10:37:32+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=18
permalink: /mikrotik-firewall-for-home-users/
frutiful_posts_template:
  - "3"
categories:
  - Uncategorized
---
Mikrotik make routers that are affordable enough for the home user market, but are quite powerful and come without too many training wheels. I'm using the RB2011UAS-2HnD-IN as a home router, wireless AP and firewall. It's powerful and configurable enough to do pretty much anything I've thrown at it - but out of the box it's probably a bit too open to attacks from randos.

<!--end_excerpt-->

Here's a firewall script I've deployed on the home gateway, with the following parameters

Home LAN Subnet - 192.168.88.0/24  
Home LAN Gateway - 192.168.88.1

<pre>/ip firewall filter
add chain=input comment="default configuration" protocol=icmp
add chain=input comment="default configuration" connection-state=established
add chain=input comment="default configuration" connection-state=related
add action=drop chain=input comment="default configuration" in-interface=sfp1-gateway
add action=drop chain=input comment="default configuration" in-interface=ether1-gateway
add chain=forward comment="default configuration" connection-state=established
add chain=forward comment="default configuration" connection-state=related
add action=drop chain=forward comment="default configuration" connection-state=invalid
add action=drop chain=input comment="Drop Invalid connections" connection-state=invalid
add chain=input comment="Allow Established connections" connection-state=established
add chain=input comment="Allow ICMP" protocol=icmp
add chain=input in-interface=!ether1-gateway src-address=192.168.88.0/24
add action=drop chain=input comment="Drop everything else"
add action=drop chain=forward comment="drop invalid connections" connection-state=invalid protocol=tcp
add chain=forward comment="allow already established connections" connection-state=established
add chain=forward comment="allow related connections" connection-state=related
add action=drop chain=forward comment="block bogon ip addresses" src-address=0.0.0.0/8
add action=drop chain=forward comment="block bogon ip addresses" dst-address=0.0.0.0/8
add action=drop chain=forward comment="block bogon ip addresses" src-address=127.0.0.0/8
add action=drop chain=forward comment="block bogon ip addresses" dst-address=127.0.0.0/8
add action=drop chain=forward comment="block bogon ip addresses" src-address=224.0.0.0/3
add action=drop chain=forward comment="block bogon ip addresses" dst-address=224.0.0.0/3
add action=jump chain=forward comment="make jumps to new chains" jump-target=tcp protocol=tcp
add action=jump chain=forward comment="make jumps to new chains" jump-target=udp protocol=udp
add action=jump chain=forward comment="make jumps to new chains" jump-target=icmp protocol=icmp
add action=drop chain=tcp comment="deny TFTP" dst-port=69 protocol=tcp
add action=drop chain=tcp comment="deny RPC portmapper" dst-port=111 protocol=tcp
add action=drop chain=tcp comment="deny RPC portmapper" dst-port=135 protocol=tcp
add action=drop chain=tcp comment="deny NBT" dst-port=137-139 protocol=tcp
add action=drop chain=tcp comment="deny cifs" dst-port=445 protocol=tcp
add action=drop chain=tcp comment="deny NFS" dst-port=2049 protocol=tcp
add action=drop chain=tcp comment="deny NetBus" dst-port=12345-12346 protocol=tcp
add action=drop chain=tcp comment="deny NetBus" dst-port=20034 protocol=tcp
add action=drop chain=tcp comment="deny BackOriffice" dst-port=3133 protocol=tcp
add action=drop chain=udp comment="deny TFTP" dst-port=69 protocol=udp
add action=drop chain=udp comment="deny PRC portmapper" dst-port=111 protocol=udp
add action=drop chain=udp comment="deny PRC portmapper" dst-port=135 protocol=udp
add action=drop chain=udp comment="deny NBT" dst-port=137-139 protocol=udp
add action=drop chain=udp comment="deny NFS" dst-port=2049 protocol=udp
add action=drop chain=udp comment="deny BackOriffice" dst-port=3133 protocol=udp
add chain=icmp comment="echo reply" icmp-options=0:0 protocol=icmp
add chain=icmp comment="net unreachable" icmp-options=3:0 protocol=icmp
add chain=icmp comment="host unreachable" icmp-options=3:1 protocol=icmp
add chain=icmp comment="host unreachable fragmentation required" icmp-options=3:4 protocol=icmp
add chain=icmp comment="allow source quench" icmp-options=4:0 protocol=icmp
add chain=icmp comment="allow echo request" icmp-options=8:0 protocol=icmp
add chain=icmp comment="allow time exceed" icmp-options=11:0 protocol=icmp
add chain=icmp comment="allow parameter bad" icmp-options=12:0 protocol=icmp
add action=drop chain=icmp comment="deny all other types"
/ip firewall nat
add action=masquerade chain=srcnat comment="default configuration" out-interface=pppoe-out1
</pre>

This firewall script can be used to somewhat lockdown access to your router/home LAN without too much struggle. It's probably missing a few bits and pieces, but seems to have put and end to hackers trying to brute-force root access via SSH over the internet - so far.