---
layout: post
title: Accessing Jetty on Ubuntu from another machine
date: '2010-04-21T23:13:00.003+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-04-21T23:22:37.654+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-142373186234879456
blogger_orig_url: http://blog.lidalia.org.uk/2010/04/ok-so-following-on-from-my-previous.html
---

OK, so following on from my [previous blog
post](2010/04/web-application-architecture-do-i.html) I thought I'd do some
performance testing to see how Jetty compares with Apache. Installed VirtualBox
on my Mac, downloaded and installed ubuntu 9.04 server edition with bridge
networking so I could access it from the host - all painless. Installed apache2
- painless. Installed jmeter on the mac - painless. Ran some performance tests.

Looked to repeat the experiment against Jetty - aargh!! Just wasted 3 hours
trying to work out why I couldn't connect to Jetty from the host. Turns out that
the documentation, in Debian/Ubuntu at least, is wrong - when /etc/init.d/jetty
claims that leaving JETTY_HOST blank will result in accepting connections from
all hosts it's lying.  You need to set it to 0.0.0.0 or it will only be
accessible from localhost (127.0.0.1). You can tell this because if you do a
`netstat -an` you will see this:
```
Proto               Local Address                          State
tcp6       0      0 127.0.0.1:8080 :::*                    LISTEN
```
that means the bind-address is localhost so the process is inaccessible outside 
the server. It should look like this:
```
Proto               Local  Address  Foreign Address         State
tcp6       0      0 :::8080         :::*                    LISTEN
```

Hope this helps some other poor confused person out there, took me a long time
to Google this... there's a Debian issue here:
http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=554874
