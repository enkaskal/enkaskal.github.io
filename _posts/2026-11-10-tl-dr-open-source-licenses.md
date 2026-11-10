---
layout: post
title: tl;dr open source licenses
date: 2026-11-10 17:38 +0000
author: enkaskal
description: not a lawyer, just some notes on open source licensing
image:
tags: [license, open source, programming]
---

My understanding is that there are essentially 2 types of philosophies:

1. [BSD](https://en.wikipedia.org/wiki/Berkeley_Software_Distribution) - [permissive](https://en.wikipedia.org/wiki/Permissive_software_license)
2. [FSF](https://en.wikipedia.org/wiki/Free_Software_Foundation) - [copyleft](https://en.wikipedia.org/wiki/Copyleft)

## permissive

The BSD philosophy is basically, [my summarization] "we want the best software out there. we don't care who owns it, how it's made, or how much it costs."

You don't have to disclose the source code if you fork/modify the code.

Permissive licenses therefore _allows_ for somebody to fork and make proprietary (or copyleft).

Common Examples include

* [BSD](https://en.wikipedia.org/wiki/BSD_licenses) - west coast
* [MIT](https://tlo.mit.edu/understand-ip/exploring-mit-open-source-license-comprehensive-guide) - east coast
* [Apache](https://www.apache.org/licenses/LICENSE-2.0) - explicitly grants the ability for companies/forkers to be able to patent it.

## copyleft

The FSF philosophy is basically, "we want software to be free to use AND modify. if you release/distribute/publish it, then the source and your modifications need to be released/distributed/published as well."

Prevents people from using/forking your code and never contributing their changes back.

**Cannot be made permissive once licensed as copyleft.**

Common Examples include

* [GPLv2](https://en.wikipedia.org/wiki/GNU_General_Public_License#Version_2) - ["i give you code, you give me changes" --Linus](https://www.youtube.com/watch?v=PaKIZ7gJlRU)
* [GPLv3](https://en.wikipedia.org/wiki/GNU_General_Public_License#Version_3) - created to combat [Tivoization](https://en.wikipedia.org/wiki/Tivoization), the creation of hardware/use of DRM to prevent users from using own software/firmware/builds on devices they've purchased.
* [AGPL](https://en.wikipedia.org/wiki/GNU_Affero_General_Public_License) - created to combat the SaaS loophole in GPL. requires source code to be published when used over a network.

## compatibility

In general, can only go one way: least permissible (e.g. public domain) -> most reciprocal (e.g. AGPLv3)

Some licenses are just incompatible [with the GPL], e.g. BSD 4-clause due to the advertising clause.

## See Also

* [Top Open Source Licenses Explained - Tiffany Jennings on mend.io]( https://www.mend.io/blog/top-open-source-licenses-explained/ )
* [Linux on Macs Has a Big License Change - Brodie Robertson on YouTube](https://www.youtube.com/watch?v=-HdgP0gkJwA)
* [License compatibility](https://en.wikipedia.org/wiki/License_compatibility)
* [GPL Compatibility & multi-licensing](https://en.wikipedia.org/wiki/GNU_General_Public_License#Compatibility_and_multi-licensing)
