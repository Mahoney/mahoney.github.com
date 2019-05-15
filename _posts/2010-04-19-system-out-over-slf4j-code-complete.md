---
layout: post
title: System Out over SLF4J Code Complete
date: '2010-04-19T19:39:00.000+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-04-19T20:01:50.322+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-5670497259294065804
blogger_orig_url: http://blog.lidalia.org.uk/2010/04/system-out-over-slf4j-code-complete.html
---

I've just completed a new [SLF4J](http://www.slf4j.org/) legacy bridging module - sysout-over-slf4j. I've donated it to SLF4J, so I'm hopeful it will soon be available as a binary there and hence in the Maven repositories. However, in the meantime anyone interested can pull it down from [my github fork](http://github.com/Mahoney/slf4j.git "sysout-over-slf4j github"); it's a simple Maven project, a quick mvn package in the root of sysout-over-slf4j should do the trick. If you're interested you could add comments here or on the [bugzilla enhancement request](http://bugzilla.slf4j.org/show_bug.cgi?id=110)

The basic idea is to transform real old school System.out.println("log message") and exception.printStacktrace() into modern logging statements that can be managed by log4j or logback or any other logging system that implements SLF4J or has a conversion layer from it, using the now standard convention of logging to a logger whose name is the same as the fully qualified name of the class doing the logging. Here's the documentation:

<h3>System.out and err over SLF4J</h3>The sysout-over-slf4j module allows a user to redirect all calls to System.out and System.err to an SLF4J defined logger with the name of the fully qualified class in which the System.out.println (or similar) call was made, at configurable levels.

<h4>What are the intended use cases?</h4>The sysout-over-slf4j module is for cases where your legacy codebase, or a third party module you use, prints directly to the console and you would like to get the benefits of a proper logging framework, with automatic capture of information like timestamp and the ability to filter which messages you are interested in seeing and control where they are sent.

The sysout-over-slf4j module is explicitly not intended to encourage the use of System.out or System.err for logging purposes. There is a significant performance overhead attached to its use, and as such it should be considered a stop-gap for your own code until you can alter it to use SLF4J directly, or a work-around for poorly behaving third party modules.

<h4>What needs to be done to make it work?</h4>sysout-over-slf4j.jar should be included on the classpath at the same level as slf4j-api.jar and your chosen slf4j implementation. A static method call sendSystemOutAndErrToSLF4J() should be made on the org.slf4j.sysoutslf4j.context.SysOutOverSLF4J class early in the life of the application to start redirecting calls to SLF4J, or the included org.slf4j.sysoutslf4j.context.SysOutOverSLF4JServletContextListener may be configured in a servlet application.

<h4>How does it work?</h4>The System.out and System.err PrintStreams are replaced with new SLF4JPrintStreams. Each time a call to System.out.println (or similar) is made, the current thread's stacktrace is examined to determine which class made the call. An SLF4J Logger named after that class's fully qualified name is retrieved and the message logged at the configured level on that logger (by default info for System.out calls and error for System.err calls).

Calls to Throwable.printStackTrace() are likewise logged at the configured level for each System output. By default there will be a message logged for every line of the stack trace; this is an unfortunate side effect of not being able reliably to retrieve the original exception that is being printed.

A servlet container may contain multiple web applications. If it has child first class loading and these applications package SLF4J in the web-app/lib directory then there will be multiple SLF4J instances running in the JVM. However, there is only one System.out and one System.err for the whole JVM. In order to ensure that the correct SLF4J instance is used for the correct web application, inside the new PrintStreams SLF4J instances are mapped against the context class loader to ensure that the same SLF4J instance used in "normal" logging is also used when calling System.out.println.

In order to prevent classloader leaks when contextsare reloaded the new PrintStreams are created by a special classloader so that they do not themselves maintain a reference to the context classloader. However, since the PrintStreams do maintain a reference to the context's SLF4J instance the user must also stop sending System.out/err to SLF4J in a context before discarding or reloading it to avoid a classloader leak via the stopSendingSystemOutAndErrToSLF4J method on the SysOutOverSLF4J class. This happens automatically if using the provided SysOutOverSLF4JServletContextListener.

<h4>Don't most logging systems print to the console? Won't that mean infinite recursion?</h4>Fortunately, Log4J, JULI &amp; Logback all do so through the write methods on PrintStream, which are rarely used for direct logging. Consequently these methods on the new PrintStreams proxy directly to the old System.out/err PrintStreams, allowing these logging frameworks to work as before.

Other SLF4J implementations may not fit this useful pattern. They can be registered with sysout-over-slf4j via static methods on the SysOutOverSLF4J class to permit them to access the console.

<h4>What about the overhead?</h4>The overhead for Log4J, JULI and Logback when printing to the console should be minimal, for the reasons outlined above. The overhead for any SLF4J implementation that needed to be registered will be greater; on every attempt by it to print to the console its fully qualified classname has to be matched against registered package names in order to determine whether it should be permitted direct access.

Finally, the overhead of actual System.out and System.err calls will be much greater, due to the expense of generating the thread's stacktrace and examining it to determine the origin of the call. As emphasised above, it would be much better if all logging were done via SLF4J directly and this module were not necessary.

