---
layout: post
title: Release of sysout-over-slf4j version 1.0.0
date: '2010-09-16T16:13:00.002+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-09-20T16:06:21.337+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-8740906931217144048
blogger_orig_url: http://blog.lidalia.org.uk/2010/09/release-of-sysout-over-slf4j-version.html
---

I'm pleased to announce the release of sysout-over-slf4j version 1.0.0, a bridge between System.out/err and SLF4J.

This effectively translates a call such as:

```java
class Foo {
 ...
    System.out.println("Hello World");
 ...
}

```

into the equivalent of:

```java
class Foo {
 ...
    LoggerFactory.getLogger(Foo.class).info("Hello World");
 ...
}

```

allowing automatic generation of logging information such as timestamp and filtering of statements using any SLF4J implementation such as Log4J or Logback.

The project page is here:

[http://projects.lidalia.org.uk/sysout-over-slf4j/](http://projects.lidalia.org.uk/sysout-over-slf4j/)

The artifacts can be downloaded here:

[http://github.com/Mahoney/sysout-over-slf4j/downloads](http://github.com/Mahoney/sysout-over-slf4j/downloads)

Or via the Maven central repository:

```xml

<dependency>
    <groupId>uk.org.lidalia</groupId>
    <artifactId>sysout-over-slf4j</artifactId>
    <version>1.0.0</version>
</dependency>

```
