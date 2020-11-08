---
id: 67
title: RYU Faucet (SDN Controller) on Raspberry Pi
date: 2016-01-20T11:28:27+00:00
author: james
excerpt: |
layout: post
guid: http://blog.dical.org/?p=67
permalink: /ryu-faucet-sdn-controller-on-raspberry-pi/
categories:
  - Uncategorized
---
I think I finally have a use for one of my 3 Raspberry Pi computers &#8211; an SDN controller.

I have an Allied Telesis switch about to arrive, which I will be using as an SDN device &#8211; buzzy and buzzwordy at the same time. I was planning on running [Ryu-Faucet](https://pypi.python.org/pypi/ryu-faucet/0.28a) as my controller stack on a VM, but I had the genius move of moving my R730 server into another location &#8211; thinking the SDN controller should be near to the switch for noob-level troubleshooting (despite that being kind of against the spirit of SDN&#8230;) &#8211; I went with the nextmost NIX thing I could find &#8211; a [Raspberry Pi 2 running Ubuntu 14.04 LTS.](https://wiki.ubuntu.com/ARM/RaspberryPi)

So &#8211; how to go about doing this from a fresh Ubuntu install? Some garbled notes to follow &#8211; but first up &#8211; I am not a software developer, I can barely code to save myself &#8211; so using things like PyPi are new to me.

First up &#8211; an apt-get update / upgrade (I run everything from root for this process)  
`<br />
james@ubuntu:/# sudo su<br />
root@ubuntu:/home/ubuntu# apt-get update<br />
root@ubuntu:/home/ubuntu# apt-get upgrade<br />
` 

That gets us up to date. On my home connection with the Raspberry Pi 2 it took about 15 minutes to complete. Next up, installing enough Python tools to install more Python tools.. Ubuntu 14.04 LTS comes with Python 2.7 by default &#8211; so that&#8217;s a good start.

Now, we grab Pip (more deets [here](https://pip.pypa.io/en/latest/installing/)):  
`<br />
root@ubuntu:/home/ubuntu# wget https://bootstrap.pypa.io/get-pip.py<br />
root@ubuntu:/home/ubuntu# python get-pip.py<br />
`  
Cool, so that grabs Pip for us. Note: I ran get-pip.py twice, the first time it installed an old version, then it smoothly upgraded it (from version 1.5 to version 7.2!). 

With Pip installed, we&#8217;re good to go installing Ryu-Faucet.. Except not quite. The instructions here don&#8217;t account for the fact that we&#8217;re not hardened Python devs (and that we&#8217;re running on an ARM CPU not an Intel one &#8211; I guess).

Grab some extra tools from apt:  
`root@ubuntu:/home/ubuntu# apt-get install python-dev`  
Now we can run Pip and pull down the file we want to install (ryu-faucet!):  
`<br />
root@ubuntu:/home/ubuntu# pip install https://pypi.python.org/packages/source/r/ryu-faucet/ryu-faucet-0.28a.tar.gz`

Watch the magic occur!

`root@ubuntu:/home/ubuntu# pip show ryu-faucet<br />
---<br />
Metadata-Version: 2.0<br />
Name: ryu-faucet<br />
Version: 0.28a0<br />
Summary: Ryu application to perform Layer 2 switching with VLANs.<br />
Home-page: http://http://openflowsdn.github.io/faucet<br />
Author: Shivaram Mysore, OpenflowSDN.Org<br />
Author-email: faucet-dev@OpenflowSDN.Org<br />
License: Apache License 2.0<br />
Location: /usr/local/lib/python2.7/dist-packages<br />
Requires: ryu, pyyaml`

Cool. Next time, we&#8217;ll configure the Pi&#8217;s single ethernet interface to handle management and SDN controller duties and fling some packets across the data plane.