---
id: 4
title: 'Self-hosted WordPress &#8211; FTP for updates/plugins'
date: 2019-04-06T14:48:32+01:00
author: james
layout: post
guid: https://blog.dical.org/?p=4
permalink: /self-hosted-wordpress-ftp-for-updates-plugins/
categories:
  - Uncategorised
---
If you are a cheapskate, and want to host WordPress yourself, on a Raspberry Pi sitting on your bookshelf &#8211; then good for you (me?). What you might find, when installing WordPress on your own LAMP box, is that installing plugins/themes etc requires an FTP account &#8211; credentials WordPress asks you for. This isn&#8217;t ideal, as in my self-hosted case I don&#8217;t have a Linux &#8216;user&#8217; specifically for my WordPress instance, and I can&#8217;t figure out the arcane magic required to set up a specific FTP user that also shares access with my /var/www/whatever.com directories, messing with groups and permissions and whatnot..

All of that out of the way &#8211; I had that problem. I tried a variety of complex methods to get the thing working, and I couldn&#8217;t find an elegant solution &#8211; until I noticed that the root directory for my Apache install&#8217;s webpages (/var/www) wasn&#8217;t owned by my web-server user (www-data on Debian 9). A quick &#8220;sudo chown -R www-data:www-data /var/www&#8221; and hey-presto, no more asking for FTP details &#8211; WordPress just works.

Hooray!