---
layout: post
title: Scala Frustrations
date: '2010-06-13T17:34:00.000+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-06-13T17:34:31.455+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2924438439221509048
blogger_orig_url: http://blog.lidalia.org.uk/2010/06/scala-frustrations.html
---

I'm learning Scala at the moment. I went looking for other "Java++" type 
languages after programming in Groovy for some time, and was very hopeful that 
Scala was what I was looking for - a type safe fully object based Java-like 
language, with clever stuff to save you typing the types. And there are lots of 
bits of it I really like - the var/val way of specifying whether a variable is 
final is nice, the object concept that allows you to have singletons that 
implement interfaces/traits, the removal of operators in favour of allowing them 
as method names. All good.

However, I'm finding it frustrating. One of the things Scala claims is that it 
has fewer special cases than Java; notably it has no primitives and no 
operators. Yet I keep on coming up against some pretty fundamental, and to me 
highly surprising, special cases.

First there's the whole notion that if you name a method ending in :, and you 
want to call it in operator notation (`instance methodname argument` rather than 
`instance.methodname(argument)`) then you call it by placing it to the left of 
the object on which you are calling it - so `argument methodname: instance`. To 
me that's a humungous special case. At the moment it seems to me that it's been 
added solely in order to make prepending to Lists more natural, because Scala 
lists are immutable and so the time taken to append apparently grows linearly 
whereas the time taken to prepend is constant. I can't help but feel that's an 
insufficient reason to introduce a massive syntax divergence from the norm. Is 
there really no other way? Would an immutable List backed by a mutable 
LinkedList (so appending returns a new view over the same List) really perform 
so badly when appending? And if it does, could it not be just a case where 
developers need to be cautious, as with String concatenation in Java?

Then there's the operator precedence rules. Scala doesn't have operators, right? 
Only methods? Wrong. It has a few method names which are special cased to have a 
different precedence order because they are normally operators in other 
languages. Nice. So for instance `(2 + 2 * 5) == 12`, whereas 
`((2).+(2).*(5)) == 20`, despite the fact that they are the same methods in the 
same order on the same objects - Scala just specifies that `2 + 2 * 5` is 
treated as `2 + (2 * 5)`. I understand this one, I'm sure it's to align Scala 
with mathematical convention, but I still disagree. Having a bunch of methods 
who behave differently in terms of order of evaluation to others based on their 
name is a huge and confusing special case.

Then there's the Tuple behaviour. You can declare a tuple as so:

```scala
val aTuple = (12, "astring")
```

but if you want to reference the first element, it's one index based!
`aTuple._1 == 12`, `aTuple._2 == "astring"`. I find this staggering - the whole 
of the rest of Scala, as a descendant of C/C++/Java, is 0 index based. 
Apparently it's because Haskell uses 1 index base for its tuples, but surely
this is a case where you should ignore Haskell's arbitrary conventions and be 
internally consistent. Hands up anyone caught out by JDBC's one index based 
behaviour?

Then there's the behaviour of `+` and `+=` on mutable and immutable Maps and 
Sets. On an immutable Map `+(Tuple)` returns a new Map instance with the Tuple 
added, as you'd expect. If you call `+=` it attempts to assign the resulting new 
Map instance to the variable, also as you'd expect - so
```scala
val aMap = Map(1 -> "one")
aMap += (2 -> "two") // cannot compile - reassigning aMap to a new instance
```
will fail to compile, because you are trying to reassign aMap. So far, so good.

On a mutable Map the behaviour of `+(Tuple)` is consistent - it returns a new 
Map instance with the new entry, it does not mutate the original one. Again, 
good. However, the behaviour of `+=` is completely inconsistent - it mutates 
the map on which it is called, but does not reassign the variable! So
```scala
val aMap = scala.collection.Map(1` -> "one")
aMap += (2 -> "two") // compiles!
```
does compile - it isn't reassigning the aMap variable, just mutating the 
underlying instance. To me that's crazy; `+=` screams "variable reassignment", 
it does not scream "mutate this instance, keep same assignment". I'd be happy if 
the `+` method mutated a mutable instance and returned a new instance on an 
immutable type, or alternatively if it was absolutely consistent and the `+` 
method always returned a new instance leaving the instance on which it was 
called unaltered and some other method (Groovy uses `<<`) were responsible for 
mutating - but either way, the `+=` operation should do exactly the same as the 
`+` operation with the caveat that it reassigns the variable to the result of 
the operation. It should not, in my opinion, ever be possible to call `+=` on an
instance stored in a `val` declared, and hence `final`, variable. The same 
confusing behaviour can be observed on Sets.


It's true that I'm very new to Scala, and in some ways this blog post is 
injudicious - perhaps if I stick with Scala in a year or two I'll look back and 
be baffled by my lack of understanding and folly in speaking up knowing so 
little. But so far I'm finding this sort of thing significantly reducing my 
enthusiasm for going further.
