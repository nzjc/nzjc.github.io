---
id: 112
title: WordPress on a VPS.. Ugh.
date: 2016-07-19T09:32:50+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=112
permalink: /wordpress-on-a-vps-ugh/
categories:
  - Uncategorized
---
As part of my dumb journey to self-host things (well, on a VPS I pay for), I fired up Apache and chucked a virtual host on it. The plan will be to move all my hosted sites to this server, but for now I'm starting with a fresh WordPress install for a family member's B&B website.

<!--end_excerpt-->

In the mix we have:

  * Apache2
  * Ubuntu 14.04 LTS 64bit
  * php5
  * MySQL
  * phpmyadmin
  * WordPress latest
  * Sweat coming out of my face

So the basic LAMP install was done without a hitch. Where WordPress started to suck was permissions. By default, somehow, I installed WordPress with the permissions set really way wide open (777 on directories).. I used some commands in the WordPress codex (inside my /var/www/sitename/public_html folder)..

james@chappie:~$ sudo find . -type f -exec chmod 664 {} +  
james@chappie:~$ sudo find . -type d -exec chmod 775 {} +  
james@chappie:~$ chmod 400 wp-config.php

So, there's that. Then I found a user couldn't upload a theme or plugin, because for some reason (despite www-data, the Ubuntu Apache user, having group rights to the files..) WordPress couldn't write to wp-content/, the folder these things go in by default.

If I ran 'su www-data touch check' inside wp-content, the file "check" was created, so it wasn't a permissions issue as far as I could tell. Weird. I ended up fixing it by explicitly telling WordPress to allow 'direct' uploading by adding:

define('FS_METHOD', 'direct');

To the wp-config.php file.. All of a sudden.. It works.

I had read elsewhere (a 3 year old mysteriously written comment on WordPress' own community site) that changing PHP's config to use FastCGI/php-fpm was the solution.. I am still (and probably always will be) a noob with these web technologies, so I wasn't really 100% sure what I was doing. I also failed, lucky 'FS_METHOD' being set to direct was what I wanted.

Update:

I found that changing the 'server api' of PHP5 to FPM/FastCGI (and removing the aformentioned FS_METHOD) also works. I followed the steps listed here (JDawg's answer): http://askubuntu.com/a/527227/521633