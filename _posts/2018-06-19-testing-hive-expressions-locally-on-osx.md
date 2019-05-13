---
layout: post
title: Testing Hive expressions locally on OS/X with brew
date: '2018-06-19T14:25:00.003+01:00'
author: Robert Elliot
tags: 
modified_time: '2018-06-19T14:25:56.099+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2626547522826253924
blogger_orig_url: http://blog.lidalia.org.uk/2018/06/testing-hive-expressions-locally-on-osx.html
---

If you want to test a hive expression (like a regex for an RLIKE clause) you can do so locally on OS/X with the following steps:

1) Run <span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">brew install hive</span>
2) Copy the following into <span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">/usr/local/opt/hive/libexec/conf/hive-site.xml</span>:

&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;

&lt;configuration&gt;
&nbsp; &lt;property&gt;
&nbsp; &nbsp; &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
&nbsp; &nbsp; &lt;value&gt;jdbc:derby:/usr/local/var/hive/metastore_db;create=true&lt;/value&gt;
&nbsp; &lt;/property&gt;
&nbsp; &lt;property&gt;
&nbsp; &nbsp; &lt;name&gt;hive.warehouse.subdir.inherit.perms&lt;/name&gt;
&nbsp; &nbsp; &lt;value&gt;false&lt;/value&gt;
&nbsp; &lt;/property&gt;
&lt;/configuration&gt;

3) Run <span style="font-family: Courier New, Courier, monospace;">schematool -initSchema -dbType derby</span>
4) Run <span style="font-family: Courier New, Courier, monospace;">hive</span>
5) At the <span style="font-family: Courier New, Courier, monospace;">hive&gt;</span> prompt, test your expression - e.g. <span style="font-family: Courier New, Courier, monospace;">select 'G:AUS:ENG:@:PT:X:' RLIKE '(G:USA:ENG:[$]:DL:X:)|(G:AUS:ENG:@:PT:X:)';</span>

You’ll get a true or false to show if your expression returned true or false.

Steps 2&amp;3 only need to be done on install.