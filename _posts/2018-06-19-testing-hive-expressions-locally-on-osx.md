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

If you want to test a hive expression (like a regex for an RLIKE clause) you can 
do so locally on OS/X with the following steps:

1) Run `brew install hive`
2) Copy the following into `/usr/local/opt/hive/libexec/conf/hive-site.xml`:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:/usr/local/var/hive/metastore_db;create=true</value>
  </property>
  <property>
    <name>hive.warehouse.subdir.inherit.perms</name>
    <value>false</value>
  </property>
</configuration>
```

3) Run `schematool -initSchema -dbType derby<`
4) Run `hive`
5) At the `hive>` prompt, test your expression - e.g. `select 
'the rain in spain' RLIKE '.+ rain .+'`

You’ll get a true or false to show if your expression returned true or false.

Steps 2 & 3 only need to be done on install.
