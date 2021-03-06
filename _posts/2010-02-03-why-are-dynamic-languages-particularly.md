---
layout: post
title: Why are dynamic languages particularly associated with being Agile?
date: '2010-02-03T19:08:00.000Z'
author: Robert Elliot
tags:
modified_time: '2010-02-03T21:31:42.140Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2048723666443699005
blogger_orig_url: http://blog.lidalia.org.uk/2010/02/why-are-dynamic-languages-particularly.html
---

It could just be me but there's quite a strong association in my mind between
Agile development methodologies and dynamic, weakly typed languages -
particularly Ruby and Groovy, though again that's possibly skewed by personal
experience.

At one level I can see it - more power can indeed sometimes allow you to produce
business value faster. There's also the testing angle; dynamic languages are
peculiarly associated with rigorous unit testing in part because you really
can't afford not to, as the compiler isn't going to catch a lot of programming
errors.

At another level, however, it seems to me that a mature, strongly typed, static
language is actually much more in tune with my understanding at least of Agile
values & principles. Fast feedback is a major part of the way the teams
I've worked on do Agile - we release weekly so that new features are in front of
the customers as soon as possible so we can evaluate whether they were a good
idea or not. We integrate continuously and run automated regression tests to
discover bugs fast. We do short (<=3 day) stories so that the results can be in
front of the business as soon as possible. We slice our stories so that QA are
looking at functional portions of them before they are complete. We test drive
from the top down using functional tests and then unit tests to find out as
early as possible what errors there might be in our code. It's all about keeping
the feedback loop as tight as possible.

And here's where it seems to me a strongly typed, static language like Java can
be _more_ "agile" - because a good IDE like Eclipse or IntelliJ will give a
much, much faster feedback loop on basic programming errors than running unit
tests. Yes, there are ways that you can speed up the running of tests (though
sadly it looks like JUnit Max has not seen wide enough adoption to keep it
going) but they still aren't going to be up there for speed with the automated
test (for that is what it is) that is a background compiler checking your code
for correctness.

Then of course there are the other benefits of a strong type system to an Agile
way of working. The code is the documentation - it should be so good, the
abstraction levels so clear and well named that you don't need anything else to
tell you how to use it. What could be more in keeping with that than compiler
checked (and therefore guaranteed never to be out of date) documentation of the
types expected and returned from a method. (That, incidentally, has always
seemed to me the primary benefit of generics - not that it saves you from
programming errors that hardly anyone ever made, but that they document what you
are expected to pass in that List argument or receive back in that Map in the
3rd party library you are using.)

In addition there's the straight forward speed of turning ideas into execution -
a good IDE with code completion saves you even making that typo in the first
place. And it can suggest to you that method name you were pretty sure was on
the class but can't quite remember (I was chatting the other day to someone who
admitted to often strongly typing his groovy variables in order to get code
completion from the IDE and then changing them back to def afterwards!)

Finally there's the limitations of tests. It's possible to overestimate the
safety given by rigorous unit testing. Don't get me wrong, it's vital, and I'm
not for a moment suggesting that a static, strongly typed language is a
rationale for not writing rigorous unit tests; just that it is inherently
impossible to write exhaustive tests that check every possible state and input,
and hence impossible to be absolutely confident in the behaviour of a piece of
code from its tests. Static, strong typing helps here. By enforcing the type of
an argument it both saves you from writing some required tests and reduces the
field of possible inputs that no-one could possibly test exhaustively. Consider
[Fantom's](http://fantom.org/) null type system - by giving you
absolute confidence as to that a method does not accept a null argument it saves
both the caller from testing its behaviour when passed null and the writer of
the method from writing a test for when null is passed in to document (and
reassure himself!) as to what occurs. Again I'd argue that the compiler is here
acting as a low level unit test in itself that is proving some of the behaviour
of your code for "free" - which speeds you up and adds quality.

I wonder how far the dynamic/agile association is actually a result of the
verbose nature of Java and its standard libraries. Returning to it from Groovy
you do notice quite how much extra boilerplate you are putting round simple
ideas, and whilst that actual typing doesn't bother me much after years making
it second nature the layer of obfuscation when I come to read it back a
day/week/month later certainly does. I've been reading and playing with Scala
lately, and whilst I have some reservations (for a language that claims to be
ridding us of Java's special cases it seems to have an awful lot of head
spinning special cases of its own...) it does demonstrate how concise and
powerful a static strongly typed language can be.

The problem as I see it at present for those like me who see real benefits in
these features is that many of the benefits listed above are realised primarily
in the presence of a really state of the art IDE. My experience of IDE support
for Fantom and Scala* (the Java++ flavours I find most appealing) is that it's a
long, long way behind say Eclipse's Java support. Which is unsurprising, but
does I feel rather undermine their selling point. The fact that their developers
_aren't_ selling them (well, not for money at any rate) may be related to that.
Still, I feel that in a perfect world language and IDE development would go hand
in hand. Anyone up for developing a new language based on incrementally altering
Eclipse's JDT?
