---
id: 116
title: 'Upgrading Junos issue - not enough space'
date: 2016-07-26T19:57:05+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=116
permalink: /upgrading-junos/
categories:
  - Uncategorized
---
Quick note, mostly for my own reference down the track.

I have in the lab a MPC3E-NG FQ and a MIC3-100G-DWDM card. To those of you not Juniper ensconced, that's a chassis-wide slot and a 100Gbit/s DWDM (tunable optic) card. Wowee, I hear you say. Anyway, the 100G DWDM card requires a fairly spicy new version of Junos, one with a new underlying FreeBSD core at the heart. My lab was running on Junos 14.1R5.5, an already pretty recent version - but for 100G across my lab I need to use the DWDM card, inconjunction with some Infinera DWDM kit for good measure.

<!--end_excerpt-->

Normally, a Junos upgrade is fairly painless. In this case however, I was getting errors that I couldn't understand. Here is how I pushed past them.

When going from 14.x to 15.1F6, Juniper don't recommend running validation of the software release against your config.

<pre class="code">james@mxZ.lab.re1&gt; request system software add /var/tmp/junos-install-mx-x86-64-15.1F6.9.tgz no-validate re1
</pre>

When I ran this on one RE (RE0) it went through just fine. On RE1 however, I got this:

<p style="padding-left: 30px;">
  Installing package '/var/tmp/junos-install-mx-x86-64-15.1F6.9.tgz' ...<br /> Verified manifest signed by PackageProductionEc_2016<br /> Verified manifest signed by PackageProductionRSA_2016<br /> Verified manifest signed by PackageProductionRSA_2016<br /> Verified contents.iso<br /> Verified issu-indb.tgz<br /> Verified junos-x86-64.tgz<br /> Verified kernel<br /> Verified metatags<br /> Verified package.xml<br /> Verified pkgtools.tgz<br /> camcontrol: not found<br /> camcontrol: not found<br /> camcontrol: not found<br /> Verified manifest signed by PackageProductionEc_2016<br /> Saving the config files ...<br /> NOTICE: uncommitted changes have been saved in /var/db/config/juniper.conf.pre-install<br /> tar: contents/jpfe-wrlinux.tgz: Wrote only 0 of 10240 bytes<br /> tar: Skipping to next header<br /> tar: Archive contains obsolescent base-64 headers<br /> tar: contents/jplatform-ex92xx.tgz: Wrote only 0 of 10240 bytes
</p>

<p style="padding-left: 30px;">
  (truncated)
</p>

<p style="padding-left: 30px;">
  tar: Skipping to next header<br /> tar: Error exit delayed from previous errors<br /> ERROR: junos-install fails post-install
</p>

This looks a bit like something has gone wrong, but it's not immediately obvious what it is. I am using a .tgz of Junos 15 that is sitting on my RE1's /var partition, and it has 2.1 GB hard-drive space free (show system storage)... Queue a few hours of headscratching.

Turns out, my assumption on how much space is actually required when upgrading from Junos 14 to 15 was completely under. I started with about 2GB free, but when I was finally successful I had 11GB free. Once the install was complete, I was down to 7.7G, which means the installation process uses up 3.3GB all by itself. I guess that's not crazy, as the tarball is 1.9GB to begin with, but the error output didn't make it clear enough for me, a stupid man.

Here is how I overcame the obvious and got Junos 15.1F6 installed 🙂

1: Jump into shell  
<!--?prettify linenums=true?-->

<pre class="code">james@mxZ.lab.re1&gt; start shell</pre>

2: Become root

<pre class="code">james@mxZ.lab.re1&gt; su
&lt;enter password&gt;</pre>

3: Find all the files that are > 500MB. We're looking for things in /var

<pre class="code">root@mxZ% find / -size +500000</pre>

4: Delete any of the old .tgz files from previous releases. If you want to keep them, SCP them off first.  
<!--?prettify linenums=true?-->

<pre class="code">root@mxZ% rm /var/sw/pkg/jinstall64-12.1R2.9-domestic-signed.tgz

etc etc</pre>

5: Check you now have > 3.5 GB free. Or go all out like me and have 11GB free, whatever.

6: Upgrade Junos and get back to work!