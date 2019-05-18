---
layout: post
title: Checked and Unchecked Exceptions
date: '2010-01-29T23:13:00.001Z'
author: Robert Elliot
tags:
modified_time: '2010-09-20T17:16:18.422+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2931876567607309453
blogger_orig_url: http://blog.lidalia.org.uk/2010/01/checked-and-unchecked-exceptions.html
---

I've just been reading this article on the hoary old topic of checked
exceptions: http://www.artima.com/intv/handcuffs.html

Professionally I currently use Groovy on a day to day basis, so I no longer have
to deal with checked exceptions at all. And I do notice the irritation factor
when I return to Java land for some home projects; indeed, looking over a recent
project I find that I just about always wrap any checked exception thrown as the
cause of a RuntimeException.

Yet in many ways I find the idea of checked exceptions very appealing - I've
been caught in the past in Groovy land with Hibernate exceptions at runtime that
frankly I should have been handling, but wasn't, and cursed the fact that
nothing made me pause to think about what could go wrong. It is an attractive
idea that the compiler should not only tell me what parameters a method expects
and what it will return, but also give me a heads up on what might go wrong that
I might like to think about.

The standard Java answer is "use checked exceptions for recoverable conditions
and unchecked for unrecoverable conditions". But this seems to me to ignore a
lesson we have all learnt over the years - you don't know what clients of your
code want or care about. Choosing what conditions your clients will regard as
recoverable and what conditions they will regard as unrecoverable at the time of
writing a library is impossible. You may make a RuntimeException which actually
they would really like to have known about, and a checked exception that they
cannot possibly handle and are quite happy to let bubble up to the top of the
stack and blow up the programme. It also ignores the third (and I would say one
of the most common) class of exceptions - the exception which is perfectly
plausible in the code that would throw it, but which the calling code happens to
know (or think it knows!) cannot logically occur. As far as the code is
concerned one of these ought really to be checked - it's an important
possibility for the writer of any client code to think about. But the writer of
the client code, having thought about it and concluded it can make the call in a
way that makes it impossible for the exception to occur, will then wish it was a
RuntimeException to save them the hassle of persuading the compiler it has been
handled.

Take ArrayIndexOutOfBoundsException. It's unchecked, yet I'd bet that it would
actually have been a profoundly useful one over the years had it been checked -
loads of thoughtless programmers must have whacked a `[0]` onto an array over
the years, blithely assuming it will always have a first element, and paid the
penalty later. A compiler complaining about a checked exception might have
encouraged them to check the array's length in code first, or at least
re-examine their assumption that the array always has a first element - perhaps
look at the documentation or the code of the array provider. And of course it is
highly likely it represents a recoverable condition in some cases.

So why was it made a RuntimeException? Because it's so massively arduous to
persuade the compiler that you _have_ thought about a checked exception. A
try/catch block with indented code is very ugly to the eye, a genuinely
expensive bit of boilerplate; putting it round every array access in order to
re-throw that checked exception as the cause of a runtime one is clearly absurd.
It would be even worse to add it to the throws clause of the method, thereby
proliferating it right the way up the stack to code that shouldn't have a clue
an array _had_ been accessed much less be trying to handle the fact that it
had been accessed wrongly.

What would be nice is some way to allow methods to declare the exceptions they
genuinely believe they might throw in a way that alerts the writer of client
code to consider those cases, without having to make a guess as to whether they
represent recoverable conditions or not for that client. Effectively, make a lot
more<sup>1</sup> exceptions checked by default. However for that to work it
has to be
really easy for the writer of the client code to indicate to the compiler "yes,
thought about it - not interested in doing anything with it". And thereby change
it from a checked to an unchecked exception, so that it can still make its way
up the stack if it does happen without forcing clients further up the stack to
pollute their methods with throws clauses or try/catch blocks for exceptions
that someone nearer the cause has already decided don't need handling beyond the
default thread ending behaviour.

So my tentative idea is to have an "unchecked" annotation, so that any line of
code preceded by @unchecked has any exceptions declared in the methods it calls
changed to runtime ones that neither need to be handled nor declared in the
methods throw signature. Something like this:
```java
class Wrapper {

    void foo() throws FooException {}

    // does not compile - unhandled FooException
    void bar1() {
        foo();
    }

    // compiles
    void bar2() {
       @unchecked
       foo();
    }
}
```

This could be extended to allow the annotation to make specific exception types
unchecked, as so:
```java

class Wrapper {

    // All 4 of these represent different conditions that may well happen
    // depending on where and how foo is called
    void foo() throws Foo1Exception, Foo2Exception, Foo3Exception, Foo4Exception {}

    // can't sensibly handle Foo1Exception in this method but it is likely to
    // happen so warn clients to think about it...
    void bar() throws Foo1Exception {

       try {
           // Nothing sensible anyone can do with these if they happen
           @unchecked({Foo3Exception.class, Foo4Exception.class})
           foo();
       } catch (Foo2Exception fe) {
           // it makes sense to handle Foo2Exception here
       }
    }
}
```

To my mind this has several benefits:
* It will discourage methods that throw the API wrapping checked MyApiException
  that actually tells you nothing about what it might represent other than
  "something went wrong in this library". We use these because it's so
  arduous to handle multiple exceptions in a throws clause, but they in many
  ways actually represent _unchecked_ exceptions - they give you no
  documentation value in terms of what the actual problem might be and what you
  might do about it. You already know the method is in library A and you already
  know that any method call can result in an exception - what have you gained,
  other than being forced to do something with it or pass the buck to whoever
  calls your method?

* The default thing for the lazy programmer to do when faced with calling a
  method that throws an exception will be to add @unchecked - easier than
  adding a throws clause that breaks calling methods, far easier than
  try/catch with an empty catch block. Which means no inconvenience to
  programmers working up the stack and no swallowed exceptions.

* It will encourage people to declare what exceptional conditions their code
  may encounter that you really might like to think about, even if they don't
  share a nice common supertype, because they will know they aren't making
  your life a misery if you aren't interested in them
* It will make it easy for the thoughtful programmer who has rightly come to
  the conclusion that there is nothing they can do with an exception (or that
  it cannot occur) to choose not to handle it
* Stack traces are more likely to start with the actual problem higher up the
  chain, rather than nested 5 down in the chain as each layer dutifully
  catches all exceptions and rethrows them in its own abstraction exception
  that tells you nothing other than that it occurred in that layer - which you
  knew from the stack trace anyway

[1] I'm not seriously suggesting ArrayIndexOutOfBoundsException should be
one of them! There are clearly going to be some types of very, very common
exceptions that have to be RuntimeExceptions or every other line of code
would be @unchecked and the result would be illegible. A programmer has to be
expected to know about and think about these without prompting, even if most
of us have failed to do so at some point.
