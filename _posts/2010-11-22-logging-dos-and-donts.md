---
layout: post
title: Logging Do's and Dont's
date: '2010-11-22T13:23:00.003Z'
author: Robert Elliot
tags:
modified_time: '2010-11-22T22:20:55.052Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-3354565573228573038
blogger_orig_url: http://blog.lidalia.org.uk/2010/11/logging-dos-and-donts.html
---

Inspired by this post on
[The Dark Art of Logging](http://blog.yohanliyanage.com/2010/11/the-dark-art-of-logging/)
I thought I'd add a few logging do's and dont's of my own.

## DO Use [SLF4J](http://www.slf4j.org/)
SLF4J is a very thin interface for logging, with multiple different actual
logging backends supported - particularly log4j, java.util.logging and logback.
You can add your own logging backend very easily indeed, or add an adaptor to an
existing logging system such as log5j. It follows a similar API to
log4j/commons-logging.

If you are writing a library you should be using it because it means you aren't
dictating a logging backend to your clients - they can use whatever they like.
If you are writing an application you should use it to insulate yourself from
future change - you can switch from log4j to logback to java.util.logging to
some other logging backend as yet unwritten at will without making code changes.

This is a very brief argument for SLF4J - there are more in depth ones
referenced [here](http://www.slf4j.org/docs.html).

## DO Use SLF4J's [Parameterized Logging](http://www.slf4j.org/faq.html#logging_performance)
Traditionally debug logging statements involving expensive string concatenation
are wrapped in a check to save the expense of generating the message when the
log is disabled:

```java

if (log.isDebugEnabled()) {
    log.debug("created user[" + user + "] with role [" + role + "]");
}

```

With SLF4J you don't need to use this rather noisy format and can get the same
performance characteristics using parameterized logging:

```java

log.debug("created user[{}] with role [{}]", user, role);

```


## DO Log the Exception Stacktrace
This is really important. Far too often you see code like this:

```java

try { ... } catch (Exception e) {
    log.error("Something went wrong: " + e); // DON'T DO THIS - LOGS THE EXCEPTION'S TOSTRING, NOT THE ENTIRE STACKTRACE
}

```

By only concatenating in the exception's toString two pieces of vital
information have been lost here; the stacktrace, which tells you where the
problem happened, but as importantly the cause. It is quite common to have
exceptions caused by other exceptions in a chain three or more deep; and
irritatingly the toString of an exception does not contain the toString of its
cause. Very often the cause was something the developer did not anticipate at
all, and so without that information you are at a huge disadvantage when
exploring a problem. When logging an exception ALWAYS do this:
```java

try { ... } catch (Exception e) {
    log.error("Something went wrong", e);
}

```
Another anti-pattern when using log4j directly is this:
```java

private static final Logger log = org.apache.log4j.Logger.getLogger(Object.class);
try { ... } catch (Exception e) {
    log.error(e); // DON'T DO THIS - LOGS THE EXCEPTION'S TOSTRING, NOT THE ENTIRE STACKTRACE
}

```
You can call log4j like this because it accepts Objects and calls toString on
them rather than accepting Strings, but the net effect is that you have once
again discarded the stacktrace and the cause of the exception, probably
accidentally. This is another good reason for using SLF4J - the code above just
won't compile because the message has to be a String in SLF4J.

A good logging backend will allow you to discard the stacktrace as part of the
logging config, just like you can discard entire messages based on their level
or logger, so this isn't forcing you to pollute your logs with endless
stacktraces - it just means that when you have an issue you can get the
stacktrace just by changing logging config, without needing to change code,
repackage and redeploy.

## DO Chain Exceptions
If you should always log the exception in order to get its cause, it follows
that when catching a low level exception and throwing a higher level exception
you should always pass the low level exception as a cause to the high level
exception. Never do this:
```java

try { ... } catch (Exception lowLevelException) {
    throw new HighLevelException("Something went wrong doing the work"); // DON'T DO THIS - NO CAUSE!
}

```
Instead do this:
```java

try { ... } catch (Exception lowLevelException) {
    throw new HighLevelException("Something went wrong doing the work", lowLevelException);
}

```


## DON'T Log Exceptions Before You Handle Them / DO Log Exceptions When You Handle Them
This might be controversial, but I am strongly of the opinion that you should
never have code like this:
```java


public static void rootMethod() {
    try {
        doHighLevelWork();
    } catch (HighLevelException highLevelException) {
        log.error("exception trying to do z", highLevelException);
        // recover
    }
}

public static void doHighLevelWork() throws LowestLevelException {
    try {
        doLowLevelWork();
    } catch (LowLevelException lowLevelException) {
        log.error("high level work went wrong because of y", lowLevelException);
        throw new HighLevelException("high level work went wrong because of y", lowLevelException);
    }
}

public static void doLowLevelWork() throws LowestLevelException {
    if ( /* something goes wrong */ ) {
        LowLevelException lowLevelException = new LowLevelException("it went wrong");
        log.error("low level work went wrong because of x", lowLevelException);
        throw lowLevelException;
    }
}

public abstract class BaseException extends Exception {
    public BaseException(String message, Throwable cause) {
        super(message, cause);
        log.error("An exception happened", this);
    }
}
public class LowLevelException extends BaseException { ... }
public class HighLevelException extends BaseException { ... }

```
By the time the root method actually recovers, we've logged the same low level
exception as error five times! And _all_ of the information was logged by the
last log.error call in the root method.

You should log an exception once and only once, otherwise when examining a log
file for stacktraces you are confused by the same data replicated multiple
times, which obfuscates the actual cause and quantity of problems. That leaves
us with the problem of when it is you should log it. I am strongly of the
opinion that you should leave it to clients of your code to log your exception
as they see fit at the point where they actually handle it (so not when
re-throwing it as the cause of a higher level exception). You
[don't know how serious clients think an exception is]({{ site.baseurl }}{% post_url 2010-01-29-checked-and-unchecked-exceptions %})
when you throw that exception. Let them decide what to do with it, including
logging it.

So what about the extra contextual information you were logging at each level?
Add it to the exception itself, ensuring that it will be printed in the
exception's message and also be available programatically to your clients. Then
when it is logged with stacktrace out will come all that contextual information,
just as you need it.

## DO Log Uncaught Exceptions
If you're going to follow the advice above then obviously it's vital that
exception does get logged at some point, so make sure your application logs
uncaught exceptions when it handles them. The default behaviour of a standalone
Java app is to call exception.printStackTrace(); you can override this as
follows:
```java

Thread.setDefaultUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
    @Override
    public void uncaughtException(Thread thread, Throwable e) {
        Logger log = LoggerFactory.getLogger(thread.getClass());
        log.error("Exception in thread [" + thread.getName() + "]", e);
    }
});

```
In a webapp you can send the uncaught exception to a jsp page, where you have
full control and can log it:
```xml
<error-page>
  <exception-type>java.lang.Exception</exception-type>
  <location>/error.jsp</location>
</error-page>
```
In Spring you can [setup exception resolvers](http://static.springsource.org/spring/docs/3.0.x/spring-framework-reference/html/mvc.html#mvc-exceptionhandlers).

## DO Use Aspect Oriented Programming for Trace Logging
If you're writing code like this:
```java

public String doSomeWork(String param1, Integer param2) {
    log.trace("> doSomeWork[param1={}, param2={}]", param1, param2);
    String result = /* work done here */;
    log.trace("< doSomeWork[{}]", result);
    return result;
}

```
in every method then it's time to find out about
[Aspect Oriented Programming](http://en.wikipedia.org/wiki/Aspect-oriented_programming).
You can use something like AspectJ to add the trace statements to all methods at
compile time, resulting in bytecode that contains the trace calls, or you can
use instrumentation to add them to the loaded class at runtime. You could even
use [Groovy AST transformations](http://groovy.codehaus.org/Local+AST+Transformations)
or [Spring proxies](http://static.springsource.org/spring/docs/3.0.x/spring-framework-reference/html/aop.html#aop-introduction-proxies),
though the latter will only add trace logging to calls between objects.
I'll try and blog about these further in future, but the net result is you still
get your trace logging but your method now looks like this:
```java

public String doSomeWork(String param1, Integer param2) {
    String result = /* work done here */;
    return result;
}

```

## DON'T Pass Sensitive Data Around as Strings
A corollary of using aspect based trace logging is that there is a grave danger
of logging sensitive information. Consider the following method:
```java

public String login(String username, String password) {
    String token = ...;
    return token;
}

```
Apply a trace aspect to this and you can turn on output like this:
```
TRACE > login[username=MYUSERNAME,password=abc123]
TRACE < login[sensitivesessiontoken]
```
Less than ideal from a security perspective!

If you follow good Object Oriented practices and wrap these values in small
classes, you can make the toString method return an obfuscated value in
production environments and an unobfuscated one in development environments,
protecting you from any accidental output from toString methods:
```java

class Password {
    private String passwordValue;
    ...
    public String toString() {
        if (env == PROD) {
            return "[password concealed]";
        } else {
            return passwordValue;
        }
    }
}

class Token {
    private String tokenValue;
    ...
    public String toString() {
        if (env == PROD) {
            return partiallyObfuscate(tokenValue);
        } else {
            return tokenValue;
        }
    }
}

public Token login(Username username, Password password) {
    Token token = ...;
    return token;
}

```
Now your log output is rather less insecure:
```
INFO > login[username=MYUSERNAME,password=[password concealed]]
INFO < login[...itivesessiont...]
```
