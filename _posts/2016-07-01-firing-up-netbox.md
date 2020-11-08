---
id: 105
title: Firing up NetBox
date: 2016-07-01T10:15:35+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=105
permalink: /firing-up-netbox/
categories:
  - Uncategorized
---
Over at [Packet Life](http://www.packetlife.net), stretch has been talking about his/Digital Ocean's cool new IPAM/DCIM. It's open source, being developed like crazy and has some interesting features (for me, the ability to document my lab setup which is getting out of hand seems like a good place to start).

<!--end_excerpt-->

I ran through the installer on [Github](https://github.com/digitalocean/netbox/blob/develop/docs/getting-started.md) and squirted the install onto a fresh Ubuntu 14.04 LTS server on AWS. Here are my thoughts...

  * The install instructions were very well written for a fresh OSS product, pretty much nothing failed the first time through. This is reminiscent of other Digital Ocean documentation I have read. I did need to jump out of editing a config file to generate a key, but that's minor.
  * From an absolute beginner with Linux pov (not quite the boat I'm in, but a boat I was fairly recently in) - there are a few things missing (like when to sudo/be root or not per command). It's not a noobs setup guide, but it is pretty easy to follow otherwise.
  * The installation/setup takes around 10-15 mins including generating the AWS EC2 host
  * The interface of NetBox looks lovely and clean

That was pretty easy and quite smooth to install. Now it's up and running I can't wait to document my lab and post the results up here.

&nbsp;