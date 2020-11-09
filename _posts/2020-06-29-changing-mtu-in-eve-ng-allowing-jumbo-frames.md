---
id: 309
title: Changing MTU in EVE-NG (allowing Jumbo frames!)
date: 2020-06-29T22:51:19+01:00
author: james
layout: post
guid: https://blog.dical.org/?p=309
permalink: /changing-mtu-in-eve-ng-allowing-jumbo-frames/
categories:
  - EVE-NG
tags:
  - eve-ng
  - mtu
  - networking
---
EVE-NG rules. As far as network simulation software goes, it's the best.

When studying or otherwise, EVE-NG is the way I prefer to try things out. One thing that happens, however, when using virtualised networks, is you obscure some underlaying things - one of them being MTU. In a [previous post](https://blog.dical.org/?p=258), I went through how the base OS that EVE-NG runs on virtualises the links between routers and switches, here I will show a way to boost the MTU these virtual network links use, so that we can throw proper jumbos across the network.

In this topology, I have 2 routers, connected with dual Ethernet links, configured in a LAG. This doesn't affect MTU at all, I just thought I'd mention it so it's not confusing.

<figure id="attachment_317" aria-describedby="caption-attachment-317" style="width: 235px" class="wp-caption aligncenter"><img loading="lazy" class="wp-image-317 size-full" src="/wp-content/uploads/2020/06/topo.png?resize=235%2C93&#038;ssl=1" alt="" width="235" height="93" data-recalc-dims="1" /><figcaption id="caption-attachment-317" class="wp-caption-text">Topology of this little lab</figcaption></figure>

The link between these routers (ae0) is set to a layer-2 MTU of 9192, which is the maximum for the platform (Juniper vSRX 3.0). This means that we should be able to send an IP packet (like a ping) of over 9000 bytes.. And yet - we can't:

<!--end_excerpt-->

<pre><code class="language-markup">root@R1&gt; ping 2.2.2.2 size 9001 do-not-fragment
PING 2.2.2.2 (2.2.2.2): 9001 data bytes

--- 2.2.2.2 ping statistics ---
4 packets transmitted, 0 packets received, 100% packet loss</code></pre>

So - let's first see why, then fix it.

If we SSH onto our EVE-NG server, and take a look at the virtual network that makes up the link between ge-0/0/1 on R1 and ge-0/0/1 on R2, as well as ge-0/0/2 on both as well (since we're in a 2 member LAG). See [previous post](https://blog.dical.org/?p=258) for more detail on how to track down which link is which and what they're called inside EVE-NG's OS:

<pre><code class="language-markdown">root@eve-ng-2:~# ifconfig vunl0_26_2
vunl0_26_2 Link encap:Ethernet  HWaddr d6:41:90:79:cb:60
          UP BROADCAST RUNNING MULTICAST  &lt;strong>MTU:9000&lt;/strong>  Metric:1
          RX packets:405 errors:0 dropped:0 overruns:0 frame:0
          TX packets:868 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:66286 (66.2 KB)  TX bytes:88080 (88.0 KB)</code></pre>

There it is. At layer-2, we have MTU 9000. As the virtual routers are assuming a piece of wire with no MTU at all connecting them to the other side, there is a problem. I think I'm MTU 9126, but in reality I'm down to 9000 on the wire. Which explains why I can send a ping that's _nearly_ 9000 bytes:

<pre><code class="language-markdown">root@R1&gt; ping 2.2.2.2 size 8976 do-not-fragment
PING 2.2.2.2 (2.2.2.2): 8976 data bytes
8984 bytes from 2.2.2.2: icmp_seq=0 ttl=64 time=1.150 ms
8984 bytes from 2.2.2.2: icmp_seq=1 ttl=64 time=1.028 ms
8984 bytes from 2.2.2.2: icmp_seq=2 ttl=64 time=1.066 ms
8984 bytes from 2.2.2.2: icmp_seq=3 ttl=64 time=1.073 ms
</code></pre>

Oh yeah. 8976 bytes at layer 3, plus IP and ethernet headers = 9000.

So - let's isolate the 4 network interfaces that EVE-NG has generated to be my ge-0/0/1's and ge-0/0/2's, then set the MTUs to something high - like 9200:

<pre><code class="language-markdown">root@eve-ng-2:~# ifconfig vunl0_26_2 mtu 9200
root@eve-ng-2:~# ifconfig vunl0_26_3 mtu 9200
root@eve-ng-2:~# ifconfig vunl0_27_2 mtu 9200
root@eve-ng-2:~# ifconfig vunl0_27_3 mtu 9200</code></pre>

When EVE-NG creates a 'wire' between two network devices, it really creates 2 virtual network interfaces and assigns each of them to a Linux bridge. In my case, with 4 total interfaces, I have two bridges. They inherit the MTU of the smallest member, so both of my bridges now should be 9200:

<pre><code class="language-markdown">root@eve-ng-2:~# ifconfig vnet0_37
vnet0_37  Link encap:Ethernet  HWaddr 0a:c8:48:ad:34:71
          UP BROADCAST RUNNING MULTICAST  MTU:9200  Metric:1
          RX packets:30596 errors:0 dropped:28163 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:3289806 (3.2 MB)  TX bytes:0 (0.0 B)</code></pre>

Alright, we can see MTU on one of the bridges is now 9200 (it was 9000 before as both contributing interfaces were 9000) and we can also see a lot of dropped packets (because all my test pings at above 8976 bytes were crashing on the digital rocks).

So.. Can we send big pings now? Man. I hope so.

<pre><code class="language-markdown">root@R1&gt; ping 2.2.2.2 size 9100 do-not-fragment
PING 2.2.2.2 (2.2.2.2): 9100 data bytes
9108 bytes from 2.2.2.2: icmp_seq=0 ttl=64 time=1.374 ms
9108 bytes from 2.2.2.2: icmp_seq=1 ttl=64 time=0.764 ms
9108 bytes from 2.2.2.2: icmp_seq=2 ttl=64 time=0.910 ms
9108 bytes from 2.2.2.2: icmp_seq=3 ttl=64 time=1.022 ms
</code></pre>

Oh yes we can.

&nbsp;

* * *

Note - this is not a permanent fix - it will last only as long as the virtual interfaces (or a server reboot) - and you need to do it for every link that is created. Perhaps it's something to submit as a feature request to the EVE-NG team - but for now this will let us live in jumbo-harmony. Network links are ephemeral in EVE-NG, so statically setting this with a config file isn't a great solution - but on the other hand we don't drop our labs all that often - do we?

&nbsp;
