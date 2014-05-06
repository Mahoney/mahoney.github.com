---
layout: default
title: Robert Elliot's views on Languages & Runtimes
description: blah
---

# Languages & Runtimes

## Philosophy

I'm a big believer in self-documenting code. I see it as a major benefit of TDD.
I'm a fan of Bob Martin's Clean Code, and I think well factored, well named code
can go a long way to being its own documentation.

I'm also of the opinion that the code *is* the detailed specification. A
specification is a precise human readable definition of what should be built.
For a specification to be precise it needs to have some formal logic to it, and
if it is a precise formally logical statement of behaviour then it must be
possible to write a computer program that can translate that statement into the
computer program it specifies - a compiler. To me there's something
fundamentally wrong if code is viewed as something that is too hard to read in
order to understand what it does, and so is in need of some other form of human
readable statement about what it does.

In order for this to work, the more a reader of the code can know by looking at
it the better. Constraints play a big part in this. Constraints let a reader
look at foreign code and, based on a good understanding of the language, already
know a great deal about what it is doing, and more importantly what it is *not*
doing - what problems they do not need to worry about.

A type system is a constraint. Garbage collection is a constraint. Immutability
is a constraint. An inflexible syntax is a constraint. Not having get-out values
like null is a constraint. Banning blocking I/O is a constraint. Forcing the
programmer to be explicit about side effects is a constraint.

All constraints of course are liable to reduce power, if only to those used to
operating without the constraint. Still, I value constraints highly, because
I find the more I can know about what a bit of code will do just by looking at
it, the happier I am. It's magic I hate.

## Java

I met a great quote the other day: ["Devs who know 1 language think it is the
one true path. Devs who know lots of languages hate them all."
](https://twitter.com/ejknapp/status/453282910892589056) Java was the language I
first learned and the one I've written the majority of my code in. Until six
years ago or so I was in the former category with Java. Now I'm increasingly
desperate to move on to something less broken, though as the rest of this page
will show I'm not fully enamoured of any of the other options. I'm also still at
that stage where my greater proficiency with Java sometimes makes it tempting to
revert to what I know, but I'm keen to break out of it...

### The good:
There's a strong sense out there of how to write good Java code, and a core set
of libraries which most developers know. Whilst it's verbose, there's a level
at which this forced verbosity makes it harder to use idioms that another
programmer won't be able to follow.

Sun & Oracle have done a fantastic job at maintaining compatibility whilst
adding features; Java 5 & 8 are tours de force at this. I think this is under
valued by programmers, if not by CTOs! It's very comforting to know that you can
almost certainly upgrade without pain to Java 8, using all your old binaries,
and start using Java 8's syntax features at once.

### The bad:
Java's default position is imperative, blocking operations on mutable shared
state that could be null. The more experience you have, the more alarming this
default is. You can work around it, of course - but it's verbose and painful to
do this, and you cannot legislate for what other people are doing in it.

## Groovy

Using Groovy in 2007 was the first time that I really got a sense of what a
better language than Java could look like, if only for the lack of verbosity &
closures. A valid Java file being all but valid Groovy helps over the hump of
learning the new way of doing things. All told I've got about 18 months of solid
Groovy development experience. It's great for dynamic access to JSON or XML.

Having said that, the dynamic characteristics, the flexible syntax and the
ability to alter the AST on the fly lead to huge temptations for devs to be
clever, and the net result is lots of magic. I hate many Groovy DSLs for this
reason; it can be a complete pain to work out what the API actually is and how
the code works. I still drop in to Groovy to knock up some quick code very
happily, however.

There's statically typed & compiled Groovy now, of course, but as ever it's not
about what you *can* do with a language but what the compiler *constrains* you
to do with the language that gives the certainty that allows you to learn faster
what someone else's code will mean.

## Scala

I've been playing with Scala for 5 or 6 years now. I've completed the [Coursera
Functional Programming Principles in Scala
](https://www.coursera.org/course/progfun) course, written a small module at
work in Scala as an experiment (it went well) and [written
](http://blog.lidalia.org.uk/2012/09/reproducing-boolean-logic-in-pure-scala.html)
quite a lot of Scala code in my spare time. It's fair to say I have a love/hate relationship with the
language.

Compiler bad error messages - tests

Null

No compiler guaranteed immutability

Binary compatibility


## ECMAScript / JavaScript

It's probably not going to be terribly surprising to learn that someone with a
bias towards statically type checked languages isn't a huge fan of ECMAScript.
There are plenty of [crazy unintuitive behaviours
](https://www.destroyallsoftware.com/talks/wat) like [variable scoping
](http://www.oreillynet.com/pub/a/javascript/excerpts/javascript-good-parts/awful-parts.html),
the ease with which you can create global variables is terrifying and I hope one
day to [understand `this`
](https://twitter.com/KentBeck/status/336874958464626688).

Still, my primary gripe is [not so much with the language per se as with the
tyrannical grip it holds on the browser
](http://blog.lidalia.org.uk/2010/09/we-need-more-language-choice-in-browser.html),
and the feeling I get that its growing popularity on the server-side owes more
to a desire to share a language between client and server-side (to exploit the
companies' existing front end resources) than to the languages' own merits.

Language strengths are heavily about constraints, and its great positive is what
it doesn't let you do - no threads, no blocking I/O. The absence of shared
mutable state and the inability to lock the server up is a very simple way to
avoid common errors, though there are drawbacks - and callback hell is its own
special form of punishment.

## Ruby & Python

I'm mentioning these primarily to say that I have run up bits of code in them;
puppet scripts & facts in Ruby, Grinder & BitBucket post commit scripts in
Python. It was a perfectly OK experience and quite fun to escape the JVM for a
bit.

I wasn't a fan of Python's rather verbose OO approach, but I really liked the
constraint of indentation. It's nice not having curly brackets or end words, and
felt very natural. I could well believe that it [makes it easier for people to
learn to code when this discipline is forced on them by the compiler
](http://okasaki.blogspot.co.uk/2008/02/in-praise-of-mandatory-indentation-for.html).

## Clojure

I've never written any Clojure in anger, which is something I plan to change
soon. I find the simplicity of the Lisp syntax attractive, and I'm deeply
convinced of the merits of building apps out of pure functions and immutable
data structures.

I'm wary of two aspects of Clojure - the lack of static type checking, and a
feeling that any Lisp, by essentially requiring the programmer to write the AST
in order to provide the simplest possible syntax whilst simultaneously
maintaining the power to use macros to alter that syntax, has possibly stepped
quite a long way towards something computer readable at the expense of human
readability. However, I'm very open to the possibility that this is merely a
matter of familiarity.

## Haskell

I feel Haskell ought to by my perfect language - a pure, lazily evaluated
functional language with a powerful, elegant statically checked type system with
full type inference. My concern is that I can't find many people prepared to run
it in production! Also, I read [strings
](https://twitter.com/aphyr/status/460845464800079872) of [tweets
](https://twitter.com/aphyr/status/460845978241605633) [like
](https://twitter.com/aphyr/status/460846286350995457) [these
](https://twitter.com/aphyr/status/460847689903509504) and on the one hand
fear that I'm going to struggle, and on the other feel that a language that
challenges people as much as this has moved a long way from my ideal of a
clearly human readable specification of a program's behaviour. I want to work in
a language other people will understand, use and preferably love - not just me!

It's still firmly on my "Languages I must learn" list, and I'd love to have the
chance to work on a real system written in it. I *want* to love this language...

## Kotlin & Ceylon

I haven't looked very hard at these languages because my first impressions were
so bad. Kotlin has [break & continue & other imperative constructs
](http://confluence.jetbrains.com/display/Kotlin/Returns+and+jumps) that I
simply can't imagine why you would include in a new language for the same reason
I wouldn't include goto in a new language.

Ceylon's [approach to null
](http://www.beyondjava.net/blog/ceylons-approach-avoids-nullpointerexception-completely/)
likewise strikes me as utterly extraordinary in a world where even Java has now
adopted the concept of Option[T]. Why you would layer syntax on to solve a
library problem I do not understand.

In both cases such basic decisions seem so flawed that I have lost interest in
exploring further.

## Others

I briefly worked with some Erlang to write map/reduce functions for [BigCouch
](http://bigcouch.cloudant.com/). I love the multi process concept, the ability
to change a running system sounds very powerful and of course it's functional
which is a Good Thing<sup>TM</sup>. Having said that, I found the syntax quite
impenetrable - couldn't get to grips with where to put a period as opposed to a
comma. More so than I think any other language I've mentioned here, so it's not
just a curly braces thing!

Sadly I've never written any Assembly, C or C++; perils of coming into the
industry in 2000 as a humanities grad. I occasionally feel the lack of context
that you get when working with a higher level abstraction over, and solution to,
problems you've never never faced yourself.

I spent a painful year writing VB / ASP over a decade ago and never plan to go
back.

## Runtimes

I've spent 13 of the last 14 years building software on the JVM. This doesn't
leave me in a great position to compare it to other runtimes. The following
aspects of it frustrate me:

* Threads are expensive - why can't we have Erlang-like lightweight processes
we just throw away?
* Why so many artificial resource constraints? Max heap size, Permgen (OK, that
one's dead...), Thread pools - it seems like half the time when performance
testing it isn't I/O, CPU, RAM or diskspace that proves the bottleneck but some
arbitrary configuration number
* It would be really good to have a solution to stop the world garbage
collection that doesn't [cost so much that they aren't prepared to tell you how
much it costs on their website
](http://www.azulsystems.com/products/zing/how-to-evaluate) and would let us use
heaps greater than 2GB.
* I wish it had native library isolation so that library A could depend on
library B for some purely internal calculation without dumping library B on the
common classpath, there to clash with some other version of library B. I know,
[OSGi gives me that...](https://twitter.com/coda/status/270978843974721536)
* Failing fast when there is more than one option to load a class from would, I
feel, prevent a multitude of undetected mines just waiting to explode months or
years down the line

The following aspects of it I like:

* Multiple language support
* Multiple platform support
* Really well understood
* Well supported monitoring via JMX

Other VMs (Ruby, Python & V8) seem lighter to start than the JVM, but I do
wonder why we don't hear as much about issues like garbage collection on them -
Ruby, JavaScript & Python all rely on garbage collection and so presumably
suffer the same stop the world issues. Do we just expect less of them? There
does seem a strong trend to move on to the JVM as companies start to experience
issues of scale.

I went to a [fascinating talk on writing VMs in PyPy
](http://tratt.net/laurie/blog/entries/fast_enough_vms_in_fast_enough_time) by
[@laurencetratt](http://twitter.com/laurencetratt) at which he claimed, from a
position of far more knowledge than me, that the JVM is not only the best VM out
there (for Java like code) but that it's unlikely anything else would ever get
near it because no company would be as mad as Sun, pouring engineering expertise
into something that makes them no money.
