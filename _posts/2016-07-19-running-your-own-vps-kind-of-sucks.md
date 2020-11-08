---
id: 109
title: Running your own VPS kind of sucks
date: 2016-07-19T08:28:44+01:00
author: james
layout: post
guid: http://blog.dical.org/?p=109
permalink: /running-your-own-vps-kind-of-sucks/
categories:
  - Uncategorized
---
I pay a good chunk of change to Dreamhost every year, I have done since I was old enough to have a credit card. It's handy. They are a pretty chill hosting provider, by and large. I host about 8 or 9 WordPress sites. Some are mine, some for friends. As I get better at unix'y stuff, I'm becoming more of a cheapskate and am thinking that $140NZ or whatever Dreamhost costs per year is a bit steep for a shared hosting platform. I have trialled their more dedicated VPS offering and while it kills performance-wise, it's too expensive for someone hosting sites on behalf of others for free...

<!--end_excerpt-->

A while back I got a good deal on [Zappie Host](http://zappiehost.com/new-zealand-vps), based in New Zealand, where most of my 'clients' are also based. It's a dedicated VPS, 2 CPU cores and a gig of RAM for about $7NZ a month. I am also on a 50% off deal for the first year, so it's a good deal. I'm paying for it in parallel with DH so I can get my shit together and migrate. Part of the motivation for sorting it all out before my next DH payment is due is financial, as I'm paying for 2 hosting solutions and not getting much benefit from the combination.

Some challenges:

  * Picking a web server (Apache or NGINX?!)
  * Picking a database (MariaDB or MySQL!?)
  * To use a cPanel or not?
  * Security
  * Mail (jeesus christ, email is complex!)
  * Virtualisation/separation of different client sites
  * Backups!?
  * Remote access for users
  * Unknown unknowns
  * DNS
  * Goddamn email
  * Resource monitoring

I think I need a coffee. What I want to do is use this list and knock out some posts detailing the crap you need to put up with to save a few bucks and call yourself a server administrator 🙂

> Time for you and time for me,  
> And time yet for a hundred indecisions,  
> And for a hundred visions and revisions,  
> Before the taking of a toast and tea.

> - T.S. Eliot (lol)