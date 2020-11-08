---
id: 52
title: Select an IPv6 address with a double-click in PuTTY
date: 2015-09-21T20:23:26+01:00
author: james
excerpt: |
layout: post
guid: http://blog.dical.org/?p=52
permalink: /select-an-ipv6-address-with-a-double-click-in-putty/
categories:
  - Uncategorized
---
This ranks highly on the nerd-alert scale, but anyway, here we are.

Putty has a nice feature where you can select a word with a double click. It's really handy, especially when you are dealing with IPv4 prefixes. It doesn’t work with IPv6 addresses by default. You can edit what Putty uses as a delimiter, under Settings -> Selection.

<!--end_excerpt-->

[<img loading="lazy" class="aligncenter size-medium wp-image-53" src="https://i2.wp.com/blog.dical.org/wp-content/uploads/2015/09/putty.png?resize=300%2C266&#038;ssl=1" alt="putty" width="300" height="266" srcset="https://i2.wp.com/blog.dical.org/wp-content/uploads/2015/09/putty.png?resize=300%2C266&ssl=1 300w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2015/09/putty.png?w=615&ssl=1 615w" sizes="(max-width: 300px) 85vw, 300px" data-recalc-dims="1" />](https://i2.wp.com/blog.dical.org/wp-content/uploads/2015/09/putty.png?ssl=1)

Scroll down to ‘:’ (or whatever character you want to change the behaviour of!) and change its class from 1 to 2. If you save this as your default settings, from now on you can get a nice double click selection of IPv6 addresses.

More info on classes <a href="http://the.earth.li/~sgtatham/putty/0.60/htmldoc/Chapter4.html#config-charclasses" target="_blank" rel="noopener noreferrer">here</a>.