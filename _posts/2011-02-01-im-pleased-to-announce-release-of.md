---
layout: post
title: Release of sysout-over-slf4j 1.0.2
date: '2011-02-01T00:09:00.001Z'
author: Robert Elliot
tags:
modified_time: '2011-02-01T13:24:25.667Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-5547756787339199276
blogger_orig_url: http://blog.lidalia.org.uk/2011/02/im-pleased-to-announce-release-of.html
---

I'm pleased to announce the release of sysout-over-slf4j version 1.0.2, a bridge
between System.out/err and SLF4J.

Release 1.0.2 fixes a concurrency issue when multiple threads were writing to a
system printstream at the same time by maintaining the contract of the existing
System out and err printstreams and synchronizing on them when printing. Also
fixes the problem with lines in a single stack trace being interleaved with
other messages printed on the same print stream.

The project page is here:
[http://projects.lidalia.org.uk/sysout-over-slf4j/](http://projects.lidalia.org.uk/sysout-over-slf4j/)

The artifacts can be downloaded here:
[http://github.com/Mahoney/sysout-over-slf4j/downloads](http://github.com/Mahoney/sysout-over-slf4j/downloads)

Or via Maven:
```xml
<dependency>
   <groupid>uk.org.lidalia</groupid>
   <artifactid>sysout-over-slf4j</artifactid>
   <version>1.0.2</version>
</dependency>
```
