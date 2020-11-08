---
id: 246
title: One of my favourite apps
date: 2019-03-27T12:49:20+00:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=246
permalink: /one-of-my-favourite-apps/
categories:
  - Uncategorized
---
Way back in the day, I had a need to browse a web service I had running at home &#8211; from work. As I didn&#8217;t really want to open the service up to every man and his dog (i.e with DST-NAT and Masq on my router) &#8211; I decided to run a SOCKS proxy on my work machine, which connects to a VM in my home network via SSH, and can then let me access this stuff &#8211; sort of like a budget version VPN. (Note &#8211; this was allowed by my employer! Don&#8217;t do it if you haven&#8217;t asked!)

The best way I could find to manage this in a painless way under Windows was called ssh-tunnel-manager. It&#8217;s a bit of open-source software you can find archived on Google Code &#8211; [here](https://code.google.com/archive/p/ssh-tunnel-manager/). It&#8217;s a simple and elegant program that is written in C# that lets you do a bunch of things, one of which is manage SSH connections to remote hosts and treat them as SOCKS proxies.<figure class="wp-block-image">

<img loading="lazy" width="784" height="564" src="https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/03/image.png?resize=784%2C564&#038;ssl=1" alt="" class="wp-image-247" srcset="https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/03/image.png?w=784&ssl=1 784w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/03/image.png?resize=300%2C216&ssl=1 300w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/03/image.png?resize=768%2C552&ssl=1 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" data-recalc-dims="1" /> <figcaption>This shows the GUI and how to add a tunnel</figcaption></figure> 

My use case, as shown above, is to SSH into a VM (vm.example.com), using an SSH key (can also use passwords, but boo!), and create a tunnel I can point my browser at to hit things in my home network (obviously you could also use this to access a dev box, cloud VM, anything really!). In the screenshot I have created a tunnel called &#8220;my_tun&#8221;, dynamic destinations and on local (to my windows machine) port 9090.<figure class="wp-block-image">

<img loading="lazy" width="778" height="819" src="https://i1.wp.com/blog.dical.org/wp-content/uploads/2019/03/image-1.png?resize=778%2C819&#038;ssl=1" alt="" class="wp-image-248" srcset="https://i1.wp.com/blog.dical.org/wp-content/uploads/2019/03/image-1.png?w=778&ssl=1 778w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2019/03/image-1.png?resize=285%2C300&ssl=1 285w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2019/03/image-1.png?resize=768%2C808&ssl=1 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" data-recalc-dims="1" /> <figcaption>Now &#8211; make your OS/browser/whatever point at the tunne</figcaption></figure> 

Once the tunnel is configured and &#8216;up&#8217;, you can point things in your OS at the SOCKS proxy localhost:9090 (here I&#8217;m showing Firefox). Neat.

The problem I have faced recently is my home VM has upgraded from Debian 8 to Debian 9. In the process, OpenSSH has been upgraded and no longer supports the out of date Key Exchange algorithms that come bundled with ssh-tunnel-manager &#8211; so you get an error message saying the SSH connection can&#8217;t be stood up. 

Lucky for us &#8211; the devs of ssh-tunnel-manager simply bundled a bunch of [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) executables that their code uses. Simply open up the directory &#8220;SSH Tunnel Manager \ Tools&#8221; and replace the 4 .exe files that come shipped with the software with recent ones from the PuTTY website &#8211; easy peasy &#8211; and it all just works again.<figure class="wp-block-image">

<img src="https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/03/image-2.png?w=840&#038;ssl=1" alt="" class="wp-image-249" data-recalc-dims="1" /> <figcaption>The replaced exes in all their glory</figcaption></figure> 

It&#8217;s a bit of a shame that the developers of ssh-tunnel-manager aren&#8217;t keeping their great software current &#8211; but lucky for us we can keep it going all by ourselves &#8211; for now!