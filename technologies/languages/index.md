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

I'm also of the opinion that the code is the detailed specification. A
specification is a precise human readable definition of what should be built. To
be precise it needs to have some formal logic to it, and there needs to be a
practical way
What gets built is a bunch of 1s and 0s only a computer can
understand.

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
keen to move on to something else! I'm still at that stage where my greater
proficiency with Java sometimes makes it tempting to revert to what I know, but
I'm keen to break out of it...

The good:
There's built up a strong sense of how to write good Java code.

## Groovy
## Scala
## ECMAScript / JavaScript

It's probably not going to be terribly surprising to learn that someone with a
bias towards strongly typed, static languages isn't a huge fan of ECMAScript.
There are plenty of [crazy unintuitive behaviours]() like [variable scoping](),
the ease with which you can creat global variables is terrifying and I hope one
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
learn to code when this discipline is forced on them by the compiler]().

## Clojure

I've never written
## Haskell
## Others

Sadly I've never written any Assembly, C or C++. I spent a painful year writing
VB / ASP.net over a decade ago

## Runtimes

I've spent 13 of the last 14 years building software on the JVM. This doesn't
leave me in a great position to compare it to other
