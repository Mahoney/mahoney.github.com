---
layout: post
title: Web Application Architecture - do I really need Apache?
date: '2010-04-19T20:36:00.000+01:00'
author: Robert Elliot
tags:
modified_time: '2010-04-19T21:15:28.151+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-3213519902086094371
blogger_orig_url: http://blog.lidalia.org.uk/2010/04/web-application-architecture-do-i.html
---

Following on from my earlier post on web application architecture, there's
another aspect of the common pattern I'm curious about - do I really need
Apache?

I had a frustrating time recently performance tuning an application. This was
partly because we were shooting at a moving target (performance test servers on
a shared virtual environment where resources may be claimed by another server at
any moment are not a great idea...), and partly because I still need to improve
my knowledge of Apache, Jetty, Linux and performance tuning methodologies.
However, I did feel that having Apache in the mix added an extra layer of
variables, measurement and configuration complexity; matching available
sockets/open files on the operating system to numbers of apache workers to
numbers of Jetty connections, bearing in mind cache hits and misses at the
Apache level, all got quite complicated.

It made me question why we need Apache. It serves three purposes in our
architecture:
1. Block external access to certain URLs
2. Put in place redirects when we, or systems we depend on, have problems
3. Act as our HTTP cache

1 & 2 we could easily do via filters at the application server layer - indeed
we're moving that way anyway, as it's then trivial to implement admin pages to
toggle these settings and it's also considerably easier to functionally test
them.

Which leaves the HTTP cache. I've been reading up on my HTTP caching recently,
particularly in an excellent book called "High Performance Web Sites". It got me
thinking, and it seemed to me that it wouldn't be that hard to implement an
internal HTTP cache in a servlet filter. A quick google revealed that
unsurprisingly I wasn't the first to think of this, it being the subject of an
[O'Reilly article](http://onjava.com/pub/a/onjava/2003/11/19/filters.html), and
indeed that [Ehcache have something along those lines](http://ehcache.org/documentation/web_caching.html).

The advantages I see to using such a cache are fivefold:
1. You get the reduced complexity of taking a layer out of your architecture.
  You no longer need to worry about how many connections & threads both Apache
  and the application server need - there's just the one to get right.

2. You get to escape from Apache's rather arcane configuration (or is it just
  me who winces in distaste whenever delving into httpd.conf?)
3. You can easily get a distributed cache. Astonishingly Apache doesn't seem to
  have an off-the-shelf distributed implementation of mod_cache, which has been
  an issue for us - we have requests for pretty rapidly changing data which we
  really need to cache for short periods of time to protect ourselves from a
  user perpetually refreshing
4. The cache will be shared across all threads in the application server,
  regardless of what form you choose to store it. Did you know that
  mod_mem_cache is a per process cache if you run Apache on a
  process-per-request basis (as RedHat does out of the box)? So loading up the
  cache for a resource has to be done on every single process - and what's
  worse, by default those processes are configured to die when idle or after a
  set period of time even if not idle to avoid a memory leak. So that resource
  you thought was being cached eternally is actually getting requested rather a
  lot. Apache recommend using mod_disk_cache instead, where the cache is shared
  across all processes, and relying on the operating system to cache it in
  memory to speed things up, but my measurements saw a drastic drop off in
  performance when we tested this. I may of course simply have got something
  wrong.
5. A personal one - as a Java dev I'm a lot happier digging into Ehcache's
  codebase to work out what it's actually up to than I would be in Apache's.

It would be quite reasonable to read that list and snort that perhaps I need to
improve my Apache skills - and perhaps that is indeed the case. However there's
a level at which that's my point; if I need to read up and gain painful
experience in order to know how to bend Apache to my will and really know what
it's up to, isn't it reasonable to look for an alternative that I will
understand better and faster?

So what are the negatives? Well, I've seen it suggested that Apache,
being native code, is faster at shoving binary data to a socket than a JVM is.
On the same basis I guess that if you are using a disk based store for your
cache Apache may be more efficient at reading data from the disk. I've no proof
for either of those theories, though, and it may equally be that modern JVMs are
pretty competitive at these things - you'd think they'd be the sort of thing Sun
had been optimising like crazy all these years. More reading and/or testing
needed.

On I presume the same basis Apache has a reputation for serving static content
much faster than an application server (this doesn't really apply to my current
project as we use [JAWR](https://jawr.dev.java.net/) for much needed efficiency
savings, and that means the application server has to serve our static content
up the first time; hopefully thereafter it's being served from a cache anyway).
I seem to remember being told, however, that modern servlet containers are
actually quite competitive on this score now (a good wikipedian would add a
nasty little [citation needed] or [where?] to that sentence!).

There may also I guess be security issues with running a servlet container or
application server on port 80; Apache has had the benefit of being the market
leader and hence the target of so many hackers that you'd hope most of the holes
have been stopped up. Though there may be an element of the safety of obscurity
by stepping away from Apache.

I'd certainly be interested in experimenting with dropping Apache, running the
servlet container on port 80 and using Ehcache or similar to do caching &
gzipping down at the Java layer. Perhaps if I do I will find out why no-one
else does!
