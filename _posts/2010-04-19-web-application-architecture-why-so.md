---
layout: post
title: Web Application Architecture - why so many nodes?
date: '2010-04-19T20:14:00.000+01:00'
author: Robert Elliot
tags:
modified_time: '2010-04-19T20:34:53.930+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2194694910282934210
blogger_orig_url: http://blog.lidalia.org.uk/2010/04/web-application-architecture-why-so.html
---

I've been thinking recently about the standard architectures we have for Java
based web applications. Obviously these do vary, but in my experience they have
some pretty common patterns:
1. Two identical legs (staging and production) that switch on each release
2. A couple of load balancers on each leg (one for fail over)
3. Multiple nodes behind these (more than 4 on a relatively high volume website)
4. Each node having a web server running Apache httpd 2.x, responsible for HTTP
   caching and possibly serving up static content when it isn't already cached
5. Each node also having an application server running a servlet container or
   J2EE application server
6. A database, typically with at least a couple of nodes for failover reasons

There are other issues, of course, such as how you manage sessions, but they
aren't what concerns me at the moment. I'm interested in questioning (from a
position admittedly of some ignorance...) an aspects of this pattern.

The first is the rationale for having more than two nodes. On my current project
we have four, about to become six. However, we are also deploying into a virtual
environment - VMWare based, where you add resources like memory and CPUs via a
console. In those circumstances I'm not entirely clear why the response to
higher load should be more nodes. Why not simply increase the resources
available to each virtual machine? Particularly in a 64bit, multiprocessor age,
surely the operating system and JVM can scale up to use the extra resources as
they are presented?

I'm partly standing here to be corrected - I'd love to be educated as to why
this assumption of mine is naive and wrong! Do sockets and open files still
represent a bottleneck in an up-to-date Linux server? Is there just a point of
diminishing returns where throwing more CPUs and more memory at a single
operating system & JVM combination doesn't scale anywhere like linearly?

I ask simply because otherwise having more than two nodes (obviously I
understand you need two for failover) seems to add a lot of complexity if it's
not actually giving benefits to match. The more servers there are the more
maintenance there is, the longer deployments take, the more you are likely to
play hunt-the-log-message from server to server to follow a user's behaviour...
you know the deal.

Now in typing that I have to acknowledge another clearly good reason to have
more than two nodes - lose one out of two and the traffic going to the other
just leapt by 100% at a time when you may well be experiencing pretty high
traffic if one of your nodes has gone down. One out of four goes down and the
others only have to cope with a 33% jump in traffic. But I'd still be interested
to know if that's the sole reason for it, or if there's a scalability issue I
need to read up on. Any book suggestions on the topic?
