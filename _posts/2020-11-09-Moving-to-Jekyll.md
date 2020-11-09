---
title: Moving to Jekyll
date: 2020-11-09T21:51:19+01:00
author: james
layout: post
permalink: /moving-to-jekyll/
categories:
  - blog
tags:
  - jekyll
  - blog
  - git
---


Since I am moving abroad soon (in 8 days), I need to put my server in a shipping container, and send it on a long voyage. That means no self-hosted Wordpress for about 3 months. With this deadline, I was forced to either move to a VPS somewhere (and pay for it), or check out Jekyll/Hugo/Ghost/whatever - the blogging platforms the cool kids are using. This is what I used to do it. You are reading the results, assuming it's not like 2025 and I've moved to something else.
<!--end_excerpt-->
The basic recipe for this website (which looks extremely plain, the way I like it):
- git
- github (and github pages)
- jekyll
- The Export to Jekyll wordpress plugin

In order to use Wordpress, which is something I've been doing since about 2004, you need a database, you need PHP and you need a bunch of plugins. What you get for all that complexity is a pretty neat CMS and blogging system, but it needs constant tending - upgrading plugins, themes and the underlaying tools. For a website like this one, which has about 300 readers a month - it's not worth the faff. Nor is paying for hosting. **Jekyll** is a nice bit of Ruby code that generates static HTML pages from Markdown, the language used in lots of open-souce software documentation. What this means is you bash out a bit of text in Markdown, run Jekyll over it and the output is a website. Once that you can host for free on Github Pages (with some caveats).

So far, so good. A fairly painless process following the Github pages docs, Jekyll docs and [this great post](https://www.bawbgale.com/from-wordpress-to-jekyll/) by Bob Gale - it took about 3 hours to convert my old Worpress to a fully running Jekyll on Github Pages site.

Good stuff:
- It's fast
- It's free
- It's clean and easy to maintain
- Free TLS cert and free hosting

Bad stuff:
- I lost a few things I liked from Wordpress (reader comments, analytics plugins)
- The whole repo is public (Github pages doesn't support private repos for free), which means any drafts etc I write I need to carefully manage separately.

Overall, pretty happy with the ROI on this. Now back to packing. 
