---
id: 4
title: 'Self-hosted WordPress - FTP for updates/plugins'
date: 2019-04-06T14:48:32+01:00
author: james
layout: post
guid: https://blog.dical.org/?p=4
permalink: /self-hosted-wordpress-ftp-for-updates-plugins/
categories:
  - Uncategorised
---
If you are a cheapskate, and want to host WordPress yourself, on a Raspberry Pi sitting on your bookshelf - then good for you (me?). What you might find, when installing WordPress on your own LAMP box, is that installing plugins/themes etc requires an FTP account - credentials WordPress asks you for. This isn't ideal, as in my self-hosted case I don't have a Linux 'user' specifically for my WordPress instance, and I can't figure out the arcane magic required to set up a specific FTP user that also shares access with my /var/www/whatever.com directories, messing with groups and permissions and whatnot..

<!--end_excerpt-->

All of that out of the way - I had that problem. I tried a variety of complex methods to get the thing working, and I couldn't find an elegant solution - until I noticed that the root directory for my Apache install's webpages (/var/www) wasn't owned by my web-server user (www-data on Debian 9). A quick "sudo chown -R www-data:www-data /var/www" and hey-presto, no more asking for FTP details - WordPress just works.

Hooray!