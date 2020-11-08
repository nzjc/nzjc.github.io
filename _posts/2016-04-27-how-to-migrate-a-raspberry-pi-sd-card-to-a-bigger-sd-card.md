---
id: 86
title: How to migrate a Raspberry Pi SD card to a bigger SD Card
date: 2016-04-27T22:40:29+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=86
permalink: /how-to-migrate-a-raspberry-pi-sd-card-to-a-bigger-sd-card/
categories:
  - Uncategorized
---
Snazzy title, James.

I recently bought a 200GB SD card from Amazon for what I consider to be a completely crazy price (around $80USD). My Raspberry Pi 3 is running OwnCloud (howto post coming soon) and the 32GB card currently serving as the OS' home is a little on the small side. It's also quite an old card, so I'm worried it will one day up and die.

Here's a quick step-by-step on how to move your SD card to a bigger one, using a Mac with OSX El Capitan (or basically any Unix based computer with an SD card reader).

<!--end_excerpt-->

**Step 0**: Backup anything important. Things can go wrong any time you mess with this stuff, so caution.

**Step 1**: Shutdown your Pi (pull the plug out)

**Step 2**: Take the SD card you want to copy out of the pi, and stick it in your Mac (with an adaptor). It will mount automatically, so run Disk Utility and unmount the 'boot' partition, which is what Raspbian OS calls it by default. Once the partitions are greyed out, they're not mounted anymore.

[<img loading="lazy" class="aligncenter size-medium wp-image-87" src="https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/04/Screen-Shot-2016-04-26-at-2.28.51-pm.png?resize=300%2C134&#038;ssl=1" alt="Screen Shot 2016-04-26 at 2.28.51 pm" width="300" height="134" srcset="https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/04/Screen-Shot-2016-04-26-at-2.28.51-pm.png?resize=300%2C134&ssl=1 300w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/04/Screen-Shot-2016-04-26-at-2.28.51-pm.png?resize=768%2C343&ssl=1 768w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/04/Screen-Shot-2016-04-26-at-2.28.51-pm.png?resize=1024%2C458&ssl=1 1024w, https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/04/Screen-Shot-2016-04-26-at-2.28.51-pm.png?w=1150&ssl=1 1150w" sizes="(max-width: 300px) 85vw, 300px" data-recalc-dims="1" />](https://i1.wp.com/blog.dical.org/wp-content/uploads/2016/04/Screen-Shot-2016-04-26-at-2.28.51-pm.png?ssl=1)

**Step 3**: Check you have space. You will need as much free hard-drive space on your Mac as equals the size of the SD card, even if it's not full. Mine is 32GB, so I need 32GB hard drive space free. Sadly, I don't have that much free on my tiny SSD, so I will be using an external USB 3 drive with ample space.

**Step 4**: Find the BSD name of your SD card reader/card. You do this by opening a terminal and entering:

<pre class="code">Jamess-MacBook-Pro:~ james$ diskutil list</pre>

Yeah which will spit out a list of your connected media. We're looking for something that matches the 32GB size of the SD Card (yours might be 8GB, 16GB... whatever)

<pre class="code">/dev/disk2
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *31.4 GB    disk2</pre>

Here we see 'disk2' under Identifier, which is what we want.

**Step 5**: Find out where the file copy of the SD card should go. I want to stick it on my external HDD called SUGAR ROSS (for some reason), so I need to find where it's mounted.

<pre class="code">Jamess-MacBook-Pro:~ james$ cd /Volumes/
Jamess-MacBook-Pro:Volumes james$ ls
BOOTCAMP    Macintosh HD    SUGAR ROSS
Jamess-MacBook-Pro:Volumes james$ cd SUGAR\ ROSS/
Jamess-MacBook-Pro:SUGAR ROSS james$ pwd
/Volumes/SUGAR ROSS</pre>

There I show you that the 1TB external drive I have connected is located at /Volumes/Sugar\ Ross/ . The extra \ I have in there is an escape character, which helps Unix based Operating Systems handle things like spaces in folder names, which it didn't historically like.

**Step 6**: Copy that floppy. We're now going to use one of the oldest Unix commands there is - dd. Once again, in the terminal, tell the computer to make a direct byte-for-byte copy of the SD card and stick it in a file called sdcard.img (or whatever you fancy).

<pre class="code">Jamess-MacBook-Pro:~ james$ sudo dd if=/dev/rdisk2 of=/Volumes/SUGAR\ ROSS/sdcard.img bs=1m</pre>

This will take a long while. It's pretty oldschool and takes its job very seriously. You can tell what's going on behind the scenes by pressing control-t every now and then. So far, mine has written 23GB in 1226 seconds, and still running. Won't be much longer now, have a cup of tea. (I will not be doing this for my 200GB card.. Hopefully).

**Step 7**: Check your image. Ok, 27 minutes elapsed time later, and dd tells me it has finished. As I never believe anything my computer tells me, I want to check the output file.

<pre class="code">Jamess-MacBook-Pro:SUGAR ROSS james$ cd /Volumes/SUGAR\ ROSS/
Jamess-MacBook-Pro:SUGAR ROSS james$ ls -la
total 61315256
d.........  1 james  staff         4096 21 Jan 19:19 $RECYCLE.BIN
d.........  1 james  staff         4096 26 Apr 14:22 .
d.........@ 5 root   admin          170 26 Apr 14:08 ..
-.........  1 james  staff  31393316864 26 Apr 14:49 sdcard.img</pre>

Using 'ls -la' shows me all the files on SUGAR ROSS, and I can see my nice .img file there at just shy of 32GB.

**Step 8**: Format wars. The SD card I am replacing my Pi's with is 200GB, and I'm told that gargantuan SD cards have a funny filesystem that's not compatible with the Pi. As I am writing a physical copy of the existing card onto the new one, I don't think I'll have a problem. If you do, I suggest formatting it to FAT32 and going from there. The dd process (this time in reverse) we just ran will take care of everything, as we didn't copy a partition of the SD card, we copied the whole thing.

**Step 9**: Prepare the new card. Stick the new (bigger) SD Card into the Mac's card reader. Use diskutil list again to find the BSD name of the disk, and then unmount any partitions that automatically mounted.

**Step 10**: Write to disk. This time, we use dd in reverse (and wait a lot longer).

<pre class="code">Jamess-MacBook-Pro:~ james$ sudo dd if=/Volumes/SUGAR\ ROSS/sdcard.img of=/dev/disk2 bs=4m</pre>

Hint: make sure you unmount, not eject the SD card partition on the card you want to write out to. Disk Utility is good for this.

**Step 11**: Fire it up. Now that we've spent 11,000 seconds writing the 32GB image back to the new card, unmount it (if you succeed with dd in step 10, it should auto mount on OSX). Place the new card into the Pi and power it up. With any luck, it will fire up as it did before. Note, you'll still only see the original filesize, for now..

<pre class="code">pi@cloud:~ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  9.7G   18G  36% /
devtmpfs        483M     0  483M   0% /dev</pre>

(See how it still says 29G? That's a good indication my old SD card (and all the partitions on it) were copied over to the 200G card.

**Step 12**: Expand the filesystem. Run the following command to stretch out the filesystem to the full 200G.

<pre class="code">pi@cloud:~ $ sudo raspi-config</pre>

You can then select 'Expand filesystem' from the menu and reboot..

**Step 13**: Revel in your success.

<pre class="code">pi@cloud:~ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       181G  9.7G  164G   6% /
devtmpfs        483M     0  483M   0% /dev</pre>

Here we see 181GiB of usable space (roughly 200GB). Hooray!