---
id: 226
title: 'Using iDRAC with a gen 11 Dell Server (on a Mac) - phew'
date: 2018-06-24T12:59:03+01:00
author: james
layout: post
guid: https://normally.online/?p=226
permalink: /using-idrac-with-a-gen-11-dell-server-on-a-mac-phew/
categories:
  - Uncategorized
---
This post is really a persistent note for me. Every now and then I end up going down the road where I need to administer a Dell server (typically one I can afford for home use, like a Dell R610) - only to find that everything I rely on at work (like having windows/java/etc) is out the door. Here are some steps to allow access to the iDRAC on Dell Rx10 server from a Mac, using Chrome as a browser.

<!--end_excerpt-->

1: Install Java SRE - https://java.com/en/download/mac_download.jsp

2: Log into the web front-end of your iDRAC (mine is at 192.168.0.195 for future reference)

3: Go to the Console/Media tab and select 'Configuration'

4: Change the plugin type from Native to Java, and disable video encryption.

<img loading="lazy" class="alignnone wp-image-228 size-large" src="https://i2.wp.com/blog.dical.org/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.34.57.png?resize=840%2C503&#038;ssl=1" alt="" width="840" height="503" srcset="https://i2.wp.com/blog.dical.org/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.34.57.png?resize=1024%2C613&ssl=1 1024w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.34.57.png?resize=300%2C180&ssl=1 300w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.34.57.png?resize=768%2C460&ssl=1 768w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.34.57.png?resize=1568%2C939&ssl=1 1568w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.34.57.png?w=1690&ssl=1 1690w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

5: Open System Preferences on your Mac, and find Java. Go to the 'Security' tab and add the https address of your iDRAC to the list of excepted sites.

<img loading="lazy" class="alignnone wp-image-229 size-large" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.36.51.png?resize=840%2C848&#038;ssl=1" alt="" width="840" height="848" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.36.51.png?resize=1015%2C1024&ssl=1 1015w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.36.51.png?resize=150%2C150&ssl=1 150w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.36.51.png?resize=297%2C300&ssl=1 297w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.36.51.png?resize=768%2C775&ssl=1 768w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-24-at-11.36.51.png?w=1180&ssl=1 1180w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

6: You need to edit this file

<pre class=""><code>/Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.security</code></pre>

And comment out the line that starts with "**jdk.tls.disabledAlgorithms"**

7: Back in the iDRAC web front-end, you can click 'Launch' on virtual console. This will download a .jnlp file (or a hideously renamed one, in my case). Rename this file viewer.jnlp (and accept OSX complaining about changing file extension).

8: Edit viewer.jnlp with a text editor (TextEdit or Nano will do) - and replace the 'user' and 'passwd' fields (which will be hashed numbers/text) with your iDRAC login details. Note - this step is optional, but it means you can open the console without having to log into iDRAC every time.

Should be good to go!