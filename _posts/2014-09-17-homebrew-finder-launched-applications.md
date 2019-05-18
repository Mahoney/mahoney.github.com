---
layout: post
title: Homebrew & Finder launched Applications
date: '2014-09-17T11:41:00.002+01:00'
author: Robert Elliot
tags:
modified_time: '2014-09-17T11:41:24.858+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-6578542448248109098
blogger_orig_url: http://blog.lidalia.org.uk/2014/09/homebrew-finder-launched-applications.html
---

Recently had an issue where scripts launched from IntelliJ did not have my
Homebrew installed executables on their path in Snow Leopard. Fixed it with the
following:
```bash
sudo sh -c 'echo "setenv PATH /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin" >> /etc/launchd.conf'
```
and restarting. No guarantees for any other machine / OS! YMMV.
