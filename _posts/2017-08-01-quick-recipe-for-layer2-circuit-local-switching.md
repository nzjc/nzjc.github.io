---
id: 188
title: Quick recipe for Layer2 Circuit local switching
date: 2017-08-01T12:45:12+01:00
author: james
layout: post
guid: https://normally.online/?p=188
permalink: /quick-recipe-for-layer2-circuit-local-switching/
categories:
  - Uncategorized
tags:
  - juniper
  - junos
  - l2circuit
  - mx
---
I am always forgetting how to do l2circuits in Juniper, partially as there seem to be a zillion ways to configure encapsulation and VLAN handling, all of which seem to commit but seemingly very few seem to work.

This is a super quick note-to-self which describes how to locally switch (could simply be extended to LDP-signalled l2circuit over an MPLS core though) a point-to-point circuit, one end VLAN tagged and the other end untagged.

<!--end_excerpt-->

For this example, we have two interfaces - both on a single MX router called mx2.lab. Our 'tagged' or NNI facing interface is xe-0/0/1, and we're using VLAN 250. Our 'untagged' or CPE facing interface is xe-2/2/1, not using a VLAN at all (dedicating the whole interface). This can (again) be expanded to use S/C tags, multiple encapsulations etc, but I'm not going there yet.

What we're aiming to see is traffic coming in on a VLAN tagged interface and being locally switched to an untagged interface. To lab this, I have a VLAN-tagged interface with IP 10.10.40.1/24 and an untagged VM, sitting on 10.10.40.2/24 - when it's configured, they should be able to ping one another.

&nbsp;

We need 3 chunks of config to make this config work:

  * The tagged interface

<pre class="lang:default decode:true">set interfaces xe-0/0/1 unit 250 description "Tagged interface for L2Circuit Test"
set interfaces xe-0/0/1 unit 250 encapsulation vlan-ccc
set interfaces xe-0/0/1 unit 250 vlan-id 250
set interfaces xe-0/0/1 unit 250 family ccc</pre>

  * The untagged interface

<pre class="lang:default decode:true">set interfaces xe-2/2/1 description "Untagged interface for L2Circuit Test"
set interfaces xe-2/2/1 mtu 9100
set interfaces xe-2/2/1 encapsulation ethernet-ccc
set interfaces xe-2/2/1 unit 0 input-vlan-map push
set interfaces xe-2/2/1 unit 0 input-vlan-map vlan-id 250
set interfaces xe-2/2/1 unit 0 output-vlan-map pop</pre>

  * The l2circuit config

<pre class="lang:default decode:true">set protocols l2circuit local-switching interface xe-0/0/1.250 end-interface interface xe-2/2/1.0
set protocols l2circuit local-switching interface xe-0/0/1.250 ignore-encapsulation-mismatch
set protocols l2circuit local-switching interface xe-0/0/1.250 ignore-mtu-mismatch
</pre>

With that config loaded on mx2.lab, packets will fly between the untagged VM on xe-2/2/1 and xe-0/0/1.250

Hoorah

<img loading="lazy" class="alignnone size-full wp-image-191" src="https://i0.wp.com/blog.dical.org/wp-content/uploads/2017/08/l2circuit-ping.png?resize=580%2C226&#038;ssl=1" alt="" width="580" height="226" srcset="https://i0.wp.com/blog.dical.org/wp-content/uploads/2017/08/l2circuit-ping.png?w=580&ssl=1 580w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2017/08/l2circuit-ping.png?resize=300%2C117&ssl=1 300w" sizes="(max-width: 580px) 85vw, 580px" data-recalc-dims="1" />