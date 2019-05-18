---
layout: post
title: Setting the Default Java File Encoding to UTF-8 on a Mac
date: '2011-04-21T12:34:00.000+01:00'
author: Robert Elliot
tags:
modified_time: '2011-04-21T12:34:44.611+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-5892664112848905155
blogger_orig_url: http://blog.lidalia.org.uk/2011/04/setting-default-java-file-encoding-to.html
---

Recently had a lot of pain with tests containing non-ASCII characters; both
Fitnesse and the GMaven* plugin for Maven were failing to read UTF-8 files
correctly because they were picking up the MacRoman character set that is the
default for Java on the Mac.

You can start java up with an argument -Dfile.encoding=UTF-8, but that's a pain
at best and very difficult if using tools that start JVMs up themselves.

However, [this answer on stackoverflow](http://stackoverflow.com/questions/361975/setting-the-default-java-character-encoding/623036#623036)
gave me a way to set it for all Java everywhere on the Mac:

`export JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF-8`
If you want this to be persistent, then add this property as explained in
[this other answer on stackoverflow](http://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x/588442#588442).

[*] There's a bug in GMaven 1.3 - it doesn't obey the
`project.build.sourceEncoding` property in Maven, for test files at any rate
