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

More for my own reference than anything else, a means to establish which java thread is using CPU time on Linux.
<ol><li>Get the process ID (PID) using ps -ef</li><li>Enter the command `top 
-p<pid>`</li><li>Press shift-h</li><li>All threads in the parent java process
 should start to appear with their lightweight process IDs and CPU usage - this takes some time</li><li>Now send a `kill -3 &lt;pid&gt;`
to the process - this will trigger a thread dump without ending the process. `CTL-\`
will also work if you have java running in a console.</li><li>Convert the PIDs of the lightweight process you are interested in into hex (so 2042 = 0x7FA)</li><li>Find the file where the Java process was sending system.out and grep for `nid=&lt;lightweight pid in hex&gt;`
</li></ol>Bingo, you should have the thread name and stack of the thread using up CPU time