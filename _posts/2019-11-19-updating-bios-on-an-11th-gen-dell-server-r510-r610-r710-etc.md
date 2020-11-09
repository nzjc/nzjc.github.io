---
id: 290
title: Step by step method for updating BIOS on an 11th gen Dell Server (R510, R610, R710 etc)
date: 2019-11-19T17:43:43+00:00
author: james
layout: post
guid: https://blog.dical.org/?p=290
permalink: /updating-bios-on-an-11th-gen-dell-server-r510-r610-r710-etc/
categories:
  - Uncategorised
---
Dell has removed the ability of 11th Gen servers to download BIOS updates from ftp.dell.com - damn. This is a problem if you care about security, but then I guess if you cared that much you'd be using a newer (read: supported) server. Unlike me, the cheapskate.

So - using Dell's Lifecycle Controller (press F10 when rebooting) - we can mount an ISO image from Dell with all the nice updates bundled in. This is very handy.

<!--end_excerpt-->

First up, boot your server into Lifecycle Controller and tell it you want to do a 'platform update':

<img loading="lazy" class="aligncenter wp-image-296 size-large" src="/wp-content/uploads/2019/11/1_lcc.png?resize=840%2C630&#038;ssl=1" alt="" width="840" height="630" srcset="/wp-content/uploads/2019/11/1_lcc.png?resize=1024%2C768&ssl=1 1024w, /wp-content/uploads/2019/11/1_lcc.png?resize=300%2C225&ssl=1 300w, /wp-content/uploads/2019/11/1_lcc.png?resize=768%2C576&ssl=1 768w, /wp-content/uploads/2019/11/1_lcc.png?w=1034&ssl=1 1034w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

&nbsp;

This is where normally you could tell the server to hijack a NIC and connect to ftp.dell.com and away you go - but no more.

Next, you will want to either attach a burned CD or USB stick, or as I will do, attach virtual media of the 'SUU' ISO to the server via iDRAC. This basically means [heading here and downloading](https://www.dell.com/support/article/uk/en/ukbsdt1/sln285500/dell-emc-server-update-utility-suu-guide-and-download) a SUU ISO for your server. They're big, between 8 and 9 GiB but they have all the goods inside. Dell themselves note you can use either the Linux or Windows variant when using Lifecycle Controller to do the upgrade - I picked Windows as it was slightly more up to date.

<img loading="lazy" class="aligncenter wp-image-297 size-large" src="/wp-content/uploads/2019/11/2_local.png?resize=840%2C627&#038;ssl=1" alt="" width="840" height="627" srcset="/wp-content/uploads/2019/11/2_local.png?resize=1024%2C764&ssl=1 1024w, /wp-content/uploads/2019/11/2_local.png?resize=300%2C224&ssl=1 300w, /wp-content/uploads/2019/11/2_local.png?resize=768%2C573&ssl=1 768w, /wp-content/uploads/2019/11/2_local.png?w=1039&ssl=1 1039w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

If the link above to the downloads fails in future, all I can suggest is you search for "Dell server SUU ISO" and hope for the best. Update 29/3/20 - [here](https://www.dell.com/support/home/uk/en/ukbsdt1/drivers/driversdetails?driverid=twh73) is a link that works as of today.

My server is remote (in the other room) and so I'll be using the Virtual Media function of iDRAC, something you can google if it sounds cool.

<img loading="lazy" class="aligncenter wp-image-293 size-full" src="/wp-content/uploads/2019/11/3_virtual_media.png?resize=582%2C207&#038;ssl=1" alt="" width="582" height="207" srcset="/wp-content/uploads/2019/11/3_virtual_media.png?w=582&ssl=1 582w, /wp-content/uploads/2019/11/3_virtual_media.png?resize=300%2C107&ssl=1 300w" sizes="(max-width: 582px) 85vw, 582px" data-recalc-dims="1" /> 

With the SUU image attached, you can select "Local Drive (CD/DVD/USB)" and hit next:

<img loading="lazy" class="aligncenter wp-image-294 size-large" src="https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/11/4_suu.png?resize=840%2C626&#038;ssl=1" alt="" width="840" height="626" srcset="https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/11/4_suu.png?resize=1024%2C763&ssl=1 1024w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/11/4_suu.png?resize=300%2C223&ssl=1 300w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/11/4_suu.png?resize=768%2C572&ssl=1 768w, https://i2.wp.com/blog.dical.org/wp-content/uploads/2019/11/4_suu.png?w=1026&ssl=1 1026w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

Neat. Hopefully your ISO name will show up and you can hit next, then play the waiting game (around 2 mins for me).

Now, you can see the list of things that need upgrading. If you expand the pic, you'll see I'm going from BIOS version 3.0.0 to 6.4.0.. Um, whoops!

<img loading="lazy" class="aligncenter wp-image-295 size-large" src="/wp-content/uploads/2019/11/5_woo.png?resize=840%2C628&#038;ssl=1" alt="" width="840" height="628" srcset="/wp-content/uploads/2019/11/5_woo.png?resize=1024%2C765&ssl=1 1024w, /wp-content/uploads/2019/11/5_woo.png?resize=300%2C224&ssl=1 300w, /wp-content/uploads/2019/11/5_woo.png?resize=768%2C574&ssl=1 768w, /wp-content/uploads/2019/11/5_woo.png?w=1028&ssl=1 1028w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

At this point, it fails, claiming non-Dell authorised updates. FSCK. Anyway, it turns out you need to be on Lifecycle Controller 1.5.2 or higher, so I need to go and grab and older version and start again, a  bit lower. I ended up using a file called [OM\_710\_SUU\_FULL\_ISO_A00.ISO](https://www.dell.com/support/home/uk/en/ukbsdt1/drivers/driversdetails?driverid=d62wh).... This is taking me to BIOS 6.2.3... Fast forward...

<img loading="lazy" class="aligncenter wp-image-298 size-large" src="/wp-content/uploads/2019/11/6_well.png?resize=840%2C628&#038;ssl=1" alt="" width="840" height="628" srcset="/wp-content/uploads/2019/11/6_well.png?resize=1024%2C765&ssl=1 1024w, /wp-content/uploads/2019/11/6_well.png?resize=300%2C224&ssl=1 300w, /wp-content/uploads/2019/11/6_well.png?resize=768%2C574&ssl=1 768w, /wp-content/uploads/2019/11/6_well.png?w=1029&ssl=1 1029w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" data-recalc-dims="1" /> 

So, it's working! I hope this is useful to the precisely 2 people on Earth who might need this.