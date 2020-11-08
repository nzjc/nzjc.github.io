---
id: 171
title: 'Openstack Newton - Provider Network Issue'
date: 2017-03-25T16:20:29+00:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=171
permalink: /openstack-newton-provider-network-issue/
categories:
  - Uncategorized
---
When playing with an Openstack POC recently, I nearly pulled my hair out. I am running a flat provider network between my compute nodes (all Ubuntu 16.04), which connect via  Cisco 2900 with an inbuilt switch module. The Cisco has gateway addresses for the dual-stack host networks. I was using native IPv6 and NAT'd private space for IPv4.

<!--end_excerpt-->

Whenever I went to launch an instance, DHCP would work (SLAAC for v6), and the Horizon front-end would show the generated addresses assigned to the instance. Looks good. Going into the console of the instance, I'd see (with ifconfig) no IP addresses on my host NIC.. Looking in the "neutron-dhcp-agent.log" log, I would see:

2017-03-24 23:49:44.169 2476 WARNING stevedore.named [req-6710e8a6-5991-446e-b8d2-5af6c9d27625 - - - - -] Could not load neutron.agent.linux.interface.BridgeInterfaceDriver

Whenever an instance was on. Cycling over and over for the number of instances. When I looked at the bridge status (in my topology the eno4 phyical interface of the compute/controller nodes are connected to the physical provider network), I would not see eno4 in the bridge created to connect hosts to physical. The way Openstack Neutron does this is to build a bridge, then add the physical and TAP interfaces). Mine was missing. Why...

Turns out - I had an IPv6 DHCP scope on my Cisco provider network interface facing the Openstack environment. As soon as I removed this piece of config (and simply left the IPv4 and IPv6 gateways on that interface) - eno4 showed up in the bridge and it all went smoothly.

What a mission.