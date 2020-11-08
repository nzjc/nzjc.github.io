---
id: 76
title: Regex for private-ASN on Junos
date: 2016-03-02T04:00:39+00:00
author: james
layout: post
guid: http://blog.dical.org/?p=76
permalink: /regex-for-private-asn-on-junos/
categories:
  - Uncategorized
---
Hi, super quick note to share something I've made to match 2 Byte Private ASN numbers (64512-65535) in Junos. You can apply it to match a community, or in an AS-PATH.

<!--end_excerpt-->

`^((6451[2-9]|645[2-9][0-9]|64[6-9][0-9][0-9]|65[0-4][0-9][0-9]|655[0-2][0-9]|6553[0-5]).*)$`

I will try and expand it to catch "0", and also 4 Byte Private ASN, but I can't burn time on that at the moment.

This one catches "0" (also an invalid community), but doesn't work in Juniper as the \D toggle doesn't compute...

`^((6451[2-9]|645[2-9][0-9]|64[6-9][0-9][0-9]|65[0-4][0-9][0-9]|655[0-2][0-9]|6553[0-5]).*)|(\D0:).*$`