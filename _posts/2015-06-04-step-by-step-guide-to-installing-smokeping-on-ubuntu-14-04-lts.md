---
id: 44
title: Step-by-Step guide to installing Smokeping on Ubuntu 14.04 LTS
date: 2015-06-04T01:15:37+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=44
permalink: /step-by-step-guide-to-installing-smokeping-on-ubuntu-14-04-lts/
frutiful_posts_template:
  - "3"
categories:
  - Uncategorized
---
Installing Smokeping on Ubuntu used to be a total breeze. Since 14.04 however, it's been a bit of a mission.

> This guide assumes a fresh out of the box Ubuntu install. I'm using the 64bit Server variety, but this should work on any 14.04 system.
> 
> UPDATE - It also works perfectly on Ubuntu 16.04 LTS Server :~)

<!--end_excerpt-->

Note - I am a noob with Apache and a relative noob with Linux, but even I got it to work fairly painlessly.

**Step 1:** install smokeping (aaand you're done).  
_ sudo apt-get install smokeping -y_

**Step 2:**  normally, you'd be done by now.. But things have changed.  
_ sudo nano /etc/smokeping/config.d/pathnames_

You're going to want to go into the _pathnames_ file and comment out the line about mail. Setting up mail to work with smokeping is outside the scope of this post. Because I'm lazy.

Do this: _#sendmail = /usr/sbin/sendmail  
_ Then hit CTRL-O, Enter, CTRL-X. That's how you save a file in Nano, I won't stick that bit in again.

**Step 3:** If you made it this far, you're going to be fine. Start 'er up...  
 _sudo service smokeping start_

**Step 4: **Head over to a web browser and enter the IP of your server/cgi-bin/smokeping.cgi  
(Hint: it will fail, giving you a 404 error).  
(Hint 2: you can find the IP address of your server by entering _ifconfig_, it's typically eth0)

**Step 5: **It's not working because you're missing a couple of things. One is a slight config change in a smokeping config file,  one is some missing symlinks.. the other is (likely) the cgi module for Apache2 isn't enabled. Fix it!

 _sudo nano /etc/smokeping/config.d/General_

Edit the line that has _cgiurl_ in it to read like:  
_cgiurl = http://10.10.10.10/cgi-bin/smokeping.cgi  
_ 

Save and quit nano.

Next you want to edit the following Apache config file:  
 _sudo nano /etc/apache2/conf-available/serve-cgi-bin.conf_

Under the lines

_<span style="line-height: 1.5;">"Require all granted<br /> </span><span style="line-height: 1.5;">     </Directory>"</span>_

You want to add:

 _ScriptAlias /smokeping/smokeping.cgi /usr/lib/cgi-bin/smokeping.cgi_  
_ Alias /smokeping /usr/share/smokeping/www_  
_ <Directory "/usr/share/smokeping/www">_  
_ Options FollowSymLinks_  
_ </Directory>_

(Save and quit editing the file)

Finally, enable CGI:  
_sudo a2enmod cgi_

**Step 6: **Kind of threw 3 things into step 5 there, whoops. Anyway now you want to restart smokeping and apache, just to see if you broke anything:  
_sudo service apache2 restart  
sudo service smokeping restart_

Now head back to your browser, throw in 10.10.10.10/cgi-bin/smokeping.cgi

It should work... If not, drop me a line.

&nbsp;