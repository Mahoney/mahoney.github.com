---
layout: post
title: Reproducing Boolean Logic in Pure Scala
date: '2012-09-24T23:54:00.000+01:00'
author: Robert Elliot
tags: 
modified_time: '2012-09-24T23:59:32.197+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2605015096737428508
blogger_orig_url: http://blog.lidalia.org.uk/2012/09/reproducing-boolean-logic-in-pure-scala.html
---

One of Scala's big claims is that the syntax is sufficiently powerful to allow programmers to produce control structures as APIs that would require dedicated language support in most languages.

It occurred to me that it would be interesting (if admittedly quixotic) to put this to the test on the most basic control structure - the humble if/else with booleans. The goal here is to implement all the normal boolean logic you would get in Java or C or the like, but without using any boolean keywords. Instead of true, false, if and else I will use true1, false1, if1 and else1 (since Scala does have these keywords, we can't use the most natural form). This is partly for fun, partly to learn Scala, partly to explore the power of the language. The full listings are at the bottom if you're not interested in the workings.

First cut - implement some basic operations on objects representing true and false. By and large this isn't really doing anything you couldn't do using an Enum in Java, with the obvious exception of the fact Scala allows you to define methods with names that would be operators. The only exception is the unary_! method; by prefixing ! with unary_ Scala allows us to put the method call before the instance it is being called on, allowing the conventional !true and !false format. By making it a sealed class we ensure no third instance of Boolean escapes into the wild.

Here are the tests:
<script src="https://gist.github.com/3778789.js?file=BooleanTest.scala"></script>

And here's the code to make them pass:
<script src="https://gist.github.com/3778797.js?file=Boolean.scala"></script>

Then we need to add the shortcut && and || to the mix. For this we use Scala's support for call by name, by prefixing the argument type to the function with =>. This means Scala will not evaluate the expression passed as this argument to the function until it is referenced within the function, unlike normal behaviour where the expression is evaluated prior to the function being called. We'll test for this:
<script src="https://gist.github.com/3778831.js?file=Boolean.scala"></script>

And make those tests pass:
<script src="https://gist.github.com/3778843.js?file=Boolean.scala"></script>

The last complication is implementing if/elseif/else behaviour. The standard form of an if statement looks very like a function taking two arguments - a boolean, followed by a function to evaluate if the boolean is true. Scala allows functions to have multiple argument lists, which allows us to mimic this form in Scala's version of a static function - one defined on a singleton object. Let's call it if1. From that function we can then return an instance of a type IfResult on which we can further call either the function elseif or the function else1 - see the tests for examples. Using the call by name form we can prevent branches being executed when we don't want them to be (and test for this, naturally). The actual choice as to what to execute can be done in the true1 and false1 instances, by using the classic strategy of polymorphism rather than conditionals.

Here are the tests:
<script src="https://gist.github.com/3778898.js?file=BooleanTest.scala"></script>

And here's the implementation:
<script src="https://gist.github.com/3778890.js?file=Boolean.scala"></script>

All told I'm pretty impressed - the only ugliness I failed to massage away was the need to have a dot between the end of the previous branch and the elseif function call. The one element of functionality I couldn't reproduce is a return statement from the enclosing function - since it happens in an expression it simply returns from that expression, and you get a compile error. If one were programming in a functional style this wouldn't be a major issue.

(Of course, there's a certain irony in reimplementing if/else if/else by using polymorphism - which is the purist OO way of avoiding if/else in the first place...)

Here are links to the complete test & implementation listings:
[BooleanTest.scala](https://gist.github.com/3778705)
[Boolean.scala](https://gist.github.com/3778716)