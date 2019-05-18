---
layout: post
title: Which Java Thread is using CPU on Linux?
date: '2010-10-11T10:00:00.001+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-10-11T10:03:30.903+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-269474878859921450
blogger_orig_url: http://blog.lidalia.org.uk/2010/10/which-java-thread-is-using-cpu-on-linux.html
---

More for my own reference than anything else, a means to establish which java 
thread is using CPU time on Linux.

1. Get the process ID (PID) using `ps -ef`
2. Enter the command `top -p<pid>`
3. Press shift-h
4. All threads in the parent java process should start to appear with their 
   lightweight process IDs and CPU usage - this takes some time
5. Now send a `kill -3 <pid>`
   to the process - this will trigger a thread dump without ending the process.
  `CTL-\` will also work if you have java running in a console.
6. Convert the PIDs of the lightweight process you are interested in into hex 
   (so 2042 = 0x7FA)
7. Find the file where the Java process was sending system.out and grep for 
   `nid=<lightweight pid in hex>`

Bingo, you should have the thread name and stack of the thread using up CPU time
