---
id: 238
title: Adding a VLAN in Ubuntu 18.04
date: 2019-02-19T16:19:05+00:00
author: james
excerpt: '						'
layout: post
guid: https://normally.online/?p=238
permalink: /adding-a-vlan-in-ubuntu-18-04/
categories:
  - Uncategorized
---
&nbsp;

For some reason, networking in Linux keeps on changing. Not only changing the well known naming scheme for ethernet interfaces (why), but now the way to manually set up IP addressing, VLANs etc in Ubuntu 18 has changed. Gone is the simple to use /etc/networking/interfaces file, and in its place some YAML and a new tool, netplan. Fine..

I needed to add a VLAN tagged interface to a physical NIC, which I used to call eth1.. So what I ended up doing was creating this YAML file in the /etc/netplan directory and putting in the following config:

<pre><code class="language-markup">james@james:/etc/netplan$ cat 1-eth1-vlan.yaml
network:
  version: 2
  ethernets:
    ens192: {}
      vlans:
        vlan999:
          id: 999
          link: ens192
          addresses: ["192.168.254.101/24"]
  routes:
    - to: 192.168.254.1/32
    via: 192.168.254.6</code></pre>

What this does is:

  * Define a network (version 2 seems to be a requirement but I haven&#8217;t looked it up)
  * Binding the VLAN to physical NIC ens192
  * Defining a VLAN, with a VLAN-ID (or &#8220;id&#8221;), an IP address
  * Putting in a static route

&nbsp;