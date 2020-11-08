---
id: 82
title: 'Owncloud 9.0.1 on Raspberry Pi 3 - Step by Step'
date: 2016-04-29T10:24:08+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=82
permalink: /owncloud-9-0-1-on-raspberry-pi-3-step-by-step/
categories:
  - Uncategorized
---
**Why?!**

I bought a Raspberry Pi 3 on the day it was announced, because I am easily excitable. When it arrived, I tried out a few things like compiling Synergy (much faster than a RPI2!) and the oblig. Quake 3. Once the fun wore off, I thought this might be a good time to finally sort out my cloud storage issues. The issues are as follows:

1) I am mildly concerned about having my data live on [someone else's computer](https://www.stickermule.com/marketplace/3442-there-is-no-cloud)  
2) I really like and _rely_ on Dropbox, but my 8GB isn't enough anymore  
3) I am a cheapskate

<!--end_excerpt-->

The solution for this is to self-host a 'cloud' storage system. While that's a bit of a paradox, having a system (with apps!) that can have my files on me wherever I go and upload pictures I take on my phone automatically is too handy to give up - and too risky to have no real control over. The best open-source (free, see point 3 above) solution I've found so far is [OwnCloud](https://owncloud.org/install/).

> Note: If you want to do an OwnCloud install following this post - it doesn't need to be on a Raspberry Pi 3 - you can do it on pretty much any Debian/Ubuntu server. One day I will move this whole thing to a proper server, but again, see point 3.

> Note 2: There are hundreds/thousands/millions of ways to do this task. I am basing this whole thing on [Dave Young's very well written howto on Element14](https://www.element14.com/community/community/raspberry-pi/raspberrypi_projects/blog/2015/05/05/owncloud-v8-server-on-raspberry-pi-2-create-your-own-dropbox). In fact, you can probably follow that right now and skip my post - I am writing this down for my own benefit and there are a \*few\* changes in OwnCloud 9.0.1

**What?**  
OK, so what do you need to get this up and running?

  1. A Raspberry Pi 3 Model B - buy one [here ](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)
  2. A MicroSD card (Class 10 is speedy, you can get a 200GB one for $80USD at the moment, which is NUTS).
  3. The .img file for Raspbian OS. I suggest using Raspbian Jesse Lite
  4. An internet connection. Any one will do, but a decent 30/10Mbit/s is probably recommended.
  5. A static IP and a domain name (or a dynamic DNS service such as the one offered at [system-ns.net](http://system-ns.net))
  6. A keyboard and an HDMI capable monitor or screen. This is just for setting up the Pi
  7. Some basic Unix shell skills.. Although you can just follow along and hopefully I'll spell everything out
  8. A router/firewall in your house that you can forward ports on

Optional extras:

  1. An external USB HDD (for more space)
  2. A smartphone, for the OwnCloud App

If you have the 8 things above (we'll cover step 4 in some detail later) - then we're good to start.

**Steps to success**

Section 1 - Setting up the Raspberry Pi 3

This section you can skip if you already have a freshly installed Raspberry Pi 3 running [Raspbian](https://www.raspbian.org/).

  1. First up, install Raspbian OS on the SD card. Steps to do this for your main PC OS are [here](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).
  2. When you have booted into your newly installed Pi, log in with username pi and password raspberry. You'll change this soon. I suggest you do this bit with the Pi plugged into a TV or screen, with a keyboard. We'll SSH in and control the Pi from another machine later on.
  3. Run the raspi-config tool - you can set your hostname, expand the file system (use all of your SD card, not just a couple of GB)  
    <!--?prettify linenums=true?--></p> 
    
    <pre class="code">pi@rasbian:~ $ sudo raspi-config
</pre>
    
    [<img loading="lazy" class=" wp-image-83 aligncenter" src="https://i0.wp.com/blog.dical.org/wp-content/uploads/2016/04/rpi-conf.png?resize=494%2C181&#038;ssl=1" alt="rpi-conf" width="494" height="181" srcset="https://i0.wp.com/blog.dical.org/wp-content/uploads/2016/04/rpi-conf.png?resize=300%2C110&ssl=1 300w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2016/04/rpi-conf.png?resize=768%2C281&ssl=1 768w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2016/04/rpi-conf.png?resize=1024%2C374&ssl=1 1024w, https://i0.wp.com/blog.dical.org/wp-content/uploads/2016/04/rpi-conf.png?w=1144&ssl=1 1144w" sizes="(max-width: 494px) 85vw, 494px" data-recalc-dims="1" />](https://i0.wp.com/blog.dical.org/wp-content/uploads/2016/04/rpi-conf.png?ssl=1)In there, I suggest you run Option 1 and 2. Inside Option 9, I set the Memory Split to 4MB (this is mostly a headless install, why waste RAM on a GPU that won't get much use), and enable SSH. I changed the hostname to 'cloud', pick a name you like. Finish up, then reboot the Pi  
    <!--?prettify linenums=true?-->
    
    <pre class="code">pi@cloud:~ $ sudo reboot</pre>

  4. Find your IP address. I am using the Ethernet interface (physically plugged into a switch), but you could use WiFi if you wanted (Raspberry Pi 3 has in-built WiFi, google will show you how to set it up!)  
    <!--?prettify linenums=true?--></p> 
    
    <pre class="code">pi@cloud:~ $ ip addr | grep eth0 | grep inet
 
   inet 192.168.0.13/24 brd 192.168.0.255 scope global eth0</pre>
    
    Here is a command to show the IP DHCP has given you (Raspbian uses DHCP by default). I could set this manually by editing /etc/network/interfaces and changing eth0 inet DHCP to inet static, but I won't be doing that. I'll be assigning a static DHCP lease in my router config to keep my Pi on 192.168.0.13 for good. ANYWAY - my IP is found, so I can SSH in from my main computer and live an easier life.</li> 
    
      * Log in to your Pi from a terminal. iTerm2 for OSX, Putty for Windows, uhh, Terminal from *nix. I use password-less entry as a security feature and because I'm lazy - if you want a hand setting that up let me know - otherwise, you're in and can run the following commands:  
        <!--?prettify linenums=true?--></p> 
        
        <pre class="code">pi@cloud:~ $ sudo usermod -a -G www-data www-data
</pre>
    
      * <pre class="code">pi@cloud:~ $ sudo apt-get install nginx openssl php5-cli php5-sqlite php5-gd php5-common php5-cgi sqlite3 php-pear php-apc php5-curl libapr1 libtool curl libcurl4-openssl-dev php-xml-parser php5 php5-dev php5-gd php5-fpm memcached php5-memcache varnish php5-apcu git</pre>
    
      * That chunky block will install some key pieces of software for us 
          1. NGINX - the web server we'll be using
          2. PHP5 - PHP, bane of all existence
          3. Lots of PHP bits and pieces, PHP5-apuc is the memory cache we'll use, for example
          4. git - to allow us to grab a nice SSL certificate from Let's Encrypt</ol> 
    
    Section 2 - Configuring and installing other bits
    
      1. Let's get an SSL cert, so browsing to our Cloud will show a nice green lock in browser, and the OwnCloud app won't complain \*too much\*. Of course, this also helps to keep the contents of the cloud machine nice and secure. I use [Let's Encrypt](https://letsencrypt.org/) for free signed certificates - something you couldn't even dream of 5 years ago. As there isn't a packaged Let's Encrypt installer for ARM7 Debian at the moment, we'll use git to grab one:  
        <!--?prettify linenums=true?--></p> 
        
        <pre class="code">cd 
git clone https://github.com/letsencrypt/letsencrypt
cd letsencrypt
./letsencrypt-auto --help</pre>
        
        This will grab Let's Encrypt (the software) and chuck it in a folder in the pi user's home directory called letsencrypt, then move us in there. It took about 10 minutes on my Pi 3 and reasonable internet connection, your results may vary.
        
        When that's installed, we need to break out and get a DNS name (and a dynamic one, at that, as my ISP doesn't offer static IP addressing)...</li> 
        
          * Head to [System NS](https://system-ns.com/), sign up and create a Dynamic DNS name. For the rest of this bit, I'll refer to my domain as cloudy.system-ns.net. Next thing we need to do is automate the IP<>DNS mapping, as my ISP might pull the rug out and change my allocated IPv4 address at any time. System NS has a guide to do this for your OS (makes sense to do it on the Raspberry Pi, though!) - which can be found [here](https://system-ns.com/services/dynamic).Basically, you create a file on the Raspberry Pi which tells the Pi to download the System NS page responsible for tying your current public IP to the chosen DNS name. Then you schedule it with crontab to run every 5 minutes. This means in the worst case, you will be a DNS orphan for around 5 minutes (System NS TTL for A records seems to be very short, which helps).
          * OK - so now we have a domain, let's get back to sorting out a SSL cert for it. As this Raspberry Pi will be used solely for OwnCloud (as far as the webserver side of things goes) I will generate the certificate for the _/var/www/owncloud_ directory:  
            <!--?prettify linenums=true?--></p> 
            
            <pre class="code">pi@cloud:~ $ sudo mkdir /var/www/owncloud
pi@cloud:~ $ cd ~
pi@cloud:~ $ cd letsencrypt/
pi@cloud:~ $ ./letsencrypt-auto certonly --webroot -w /var/www/owncloud -d cloudy.system-ns.net</pre>
            
            This will go away and pull a cert down from Let's Encrypt. In fact, it will pull down a public certificate and a private key. They will be (by default) lurking in _/etc/letsencrypt/live/cloudy.system-ns.net_ (you need to be root to look in there, which you can do with 'sudo su'. Type 'exit' to get back to the pi user when you're done gawking at the nice new certs.</li> 
            
              * So, that's some housekeeping done. Next, I'll steal wholesale from Dave, and give a modified version of his NGINX config (remember, that's the web server we installed back in section 1).Let's edit the nginx config file! (Actually, let's delete everything in it and start again)!  
                <!--?prettify linenums=true?--></p> 
                
                <pre class="code">pi@cloud:~ $ sudo nano /etc/nginx/sites-available/default</pre>
                
                This will open nano, the wusses text editor. I love it, because I can remember how to use it in a hurry. Anyway, delete everything in there (ctrl-k deletes a whole line at a time in nano).  Then edit this, and throw it in:  
                <!--?prettify linenums=true?-->
                
                <pre class="code">upstream php-handler {
server 127.0.0.1:9000;
#server unix:/var/run/php5-fpm.sock;
}

server {
listen 80;
server_name cloudy.system-ns.net;
return 301 https://$server_name$request_uri;  # enforce https
}

server {
listen 443 ssl;
server_name cloudy.system-ns.net&gt;;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

ssl_certificate /etc/letsencrypt/live/cloudy.system-ns.net/cert.pem;
ssl_certificate_key /etc/letsencrypt/live/cloudy.system-ns.net/privkey.pem;

# Path to the root of your installation
root /var/www/owncloud;

client_max_body_size 2000M; # set max upload size
fastcgi_buffers 64 4K;

rewrite ^/caldav(.*)$ /remote.php/caldav$1 redirect;
rewrite ^/carddav(.*)$ /remote.php/carddav$1 redirect;
rewrite ^/webdav(.*)$ /remote.php/webdav$1 redirect;

index index.php;
error_page 403 /core/templates/403.php;
error_page 404 /core/templates/404.php;

location = /robots.txt {
allow all;
log_not_found off;
access_log off;
}

location ~ ^/(?:\.htaccess|data|config|db_structure\.xml|README) {
deny all;
}
location / {
                # The following 2 rules are only needed with webfinger
                rewrite ^/.well-known/host-meta /public.php?service=host-meta last;
                rewrite ^/.well-known/host-meta.json /public.php?service=host-meta-json last;

                rewrite ^/.well-known/carddav /remote.php/carddav/ redirect;
                rewrite ^/.well-known/caldav /remote.php/caldav/ redirect;

                rewrite ^(/core/doc/[^\/]+/)$ $1/index.html;

                try_files $uri $uri/ index.php;
}

location ~ \.php(?:$|/) {
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param PATH_INFO $fastcgi_path_info;
                fastcgi_param HTTPS on;
                fastcgi_pass php-handler;
}

# Optional: set long EXPIRES header on static assets
location ~* \.(?:jpg|jpeg|gif|bmp|ico|png|css|js|swf)$ {
                expires 30d;
                # Optional: Don't log access to assets
                access_log off;
}

}</pre>
                
                You can copy mine and replace all the bits with cloudy, 192.168.0.13 etc..</li> 
                
                  * Now we can change the PHP settings to allow for larger uploads of files. Dave suggests 2GB, so we'll go with that. In practice you can set it so whatever your filesystem allows. I'll combo in a couple of steps here (changing where php-fqm listens as well)  
                    <!--?prettify linenums=true?--></p> 
                    
                    <pre class="code">sudo nano /etc/php5/fpm/php.ini
search for upload_max_filesize and set it to 2000M
search for post_max_size and set it to 2000M

sudo nano /etc/php5/fpm/pool.d/www.conf
Then Change:
listen = /var/run/php5-fpm.sock 
to :
listen = 127.0.0.1:9000

sudo nano /etc/dphys-swapfile
Then Change:
CONF_SWAPSIZE=100 
to :
CONF_SWAPSIZE=512</pre>
                    
                    I didn't follow all of these exactly, so I'll copy-pasta Dave's advice and leave you to try it out.</li> 
                    
                      * Reboot the Pi!  
                        <!--?prettify linenums=true?--></p> 
                        
                        <pre class="code">sudo reboot
[enter]</pre>
                    
                      * Now you need to install OwnCloud (hey, finally!). The way to do it on a Raspberry Pi 3 running Raspbian is simple. 
                          1. Grab the tarball from [here](https://owncloud.org/install/#instructions-server)  
                            <!--?prettify linenums=true?--></p> 
                            
                            <pre class="code">pi@cloud:~ $ cd
pi@cloud:~ $ mkdir downloads
pi@cloud:~ $ cd downloads/
pi@cloud:~/downloads $ wget https://download.owncloud.org/community/owncloud-9.0.1.tar.bz2</pre>
                        
                          2. Extract the files  
                            <!--?prettify linenums=true?--></p> 
                            
                            <pre class="code">pi@cloud:~ $ sudo tar xvf owncloud-9.0.1.tar.bz2</pre>
                        
                          3. Now you end up with a nice folder called 'owncloud'. We want to stick that where NGINX is looking for websites, so we'll move it to /var/www, change the ownership of the folder to belong to the www-data user and delete all the evidence.  
                            <!--?prettify linenums=true?--></p> 
                            
                            <pre class="code">pi@cloud:~/downloads $ sudo mv owncloud/ /var/www
pi@cloud:~/downloads $ sudo chown -R www-data:www-data /var/www
pi@cloud:~/downloads $ rm -rf owncloud*</pre>
                        
                          4. Dave now recommends you edit a couple of files in the /var/www/owncloud folder to tweak the filesizes:  
                            <!--?prettify linenums=true?--></p> 
                            
                            <pre class="code">pi@cloud:~/ $ sudo nano .htaccess 
Then set the following values to 2000M: 
Php_value upload_max_filesize 
Php_value post_max_size 
Php_value memory_limit 

</pre>
                            
                            <pre class="code">pi@cloud:~/ $ sudo nano .user.ini 
Then set the following values to 2000M: upload_max_filesize 
post_max_size 
memory_limit</pre>
                        
                          5. Do a final 'sudo reboot'
                          6. Browse to your 192.168.0.13 (whatever you called it) and set up OwnCloud. I have mine set to use the SD card for storage, if you wish to use an external HDD, consult [Dave's excellent post](https://www.element14.com/community/community/raspberry-pi/raspberrypi_projects/blog/2015/05/05/owncloud-v8-server-on-raspberry-pi-2-create-your-own-dropbox).
                      * When you first load OwnCloud it might complain about a few things, they can usually be solved by installing a memcache, tightening up file/folder permissions or tweaking other security features. As you have a valid SSL cert, you can probably get going pretty well straight away. I've added the necessary tweaks to the NGINX config that should get you past most of the pains, at least under Owncloud 9.0.1.
                      * That should be it! I lied, above, though. I use both the SD card and a backup onto an external HDD. This gives me the ability to live without my external HDD if I need it for anything temporarily, and keep 2 copies locally of all my stuff. To do it, I use a nice program called rsync and a little script + crontab to schedule it to run. rsync has a million uses, but one is copying files from one place to another in a really smart way, using deltas (i.e. it only copies the files that have changed). This way, I can schedule it to run every 6 hours, and it only takes as long as transfering the new or changed files to run. 
                          1. Install rsync!  
                            <!--?prettify linenums=true?--></p> 
                            
                            <pre class="code">pi@cloud:~ $ sudo apt-get install rsync</pre>
                        
                          2. Create a script to run the sync..  
                            <!--?prettify linenums=true?--></p> 
                            
                            <pre class="code">pi@cloud:~ $ mkdir scripts
pi@cloud:~ $ cd scripts
pi@cloud:~ $ nano oc-backup.sh

#!/bin/bash

date
sudo rsync -avz /var/www/owncloud/ /media/shamu/oc-backup/ | tail -n 2
exit 0

pi@cloud:~ $ chmod +x oc-backup.sh
pi@cloud:~ $ crontab -e

Add the following to crontab (which is a bit like nano, or vi, depending on what you select the first time you run it)

0 0,6,12,18 * * * ~/scripts/oc-backup.sh &gt;&gt; /media/shamu/oc-backup.log</pre>
                        
                          3. Now every 6 hours from midnight that will run, doing an rsync and adding a date-stamped line to the file oc-backup.log. My 1TB external drive is permanently mounted to /media/shamu, by the way.</ol> 
                    
                    So, that's it. Up and running. Mine has been working for 2 weeks now, approx 128MB of RAM free during normal operation (I check a little too often). php-fqm eats the CPU for breakfast when doing things like generating image previews in the app (I use the Android one and it's great).. But mostly it all works. In fact, today I deleted my Dropbox app and all the files on it - I'm eating my own dogfood here, trusting this setup for better or worse. If you use it, let me know!