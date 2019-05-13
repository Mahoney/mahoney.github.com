---
layout: post
title: Scala's Maligned Type System
date: '2012-10-24T12:21:00.000+01:00'
author: Robert Elliot
tags: 
modified_time: '2012-10-24T12:21:07.649+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-7634682276504768981
blogger_orig_url: http://blog.lidalia.org.uk/2012/10/scalas-maligned-type-system.html
---

Around the net you can <a href="http://blog.joda.org/2011/11/scala-feels-like-ejb-2-and-other.html">find a lot of criticisms of Scala's type system</a>, often focussing on this signature:

<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">def ++ [B &gt;: A, That] (that: TraversableOnce[B])(implicit bf: CanBuildFrom[List[A], B, That]) : That</pre>
Before we start, let's make one thing clear - I am not arguing that this is trivial for a Java developer to read. There are a lot of barriers to legibility for a Java dev here, which I'll try and unpick below. My issue with this is as an example of type system complexity, since as far as I can see the difficulties with legibility here are nothing to do with the type system - at least for a Java developer. What's going on here, from a type perspective, is no more complicated than Java's generics permits.

In an attempt to prove this, let's "undo" the non-type system syntax features that make this difficult to read for a Java developer.

1) Prefix types rather than postfix types
Scala puts types after variable / method signatures rather than before. Let's switch back to the java way:
<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">That ++ [B &gt;: A, That] (TraversableOnce[B] that)(implicit CanBuildFrom[List[A], B, That] bf)</pre>
2) Multiple parameter lists
Scala allows multiple parameter lists for a function/method, so lets collapse them into one:
<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">That ++ [B &gt;: A, That] (TraversableOnce[B] that, implicit CanBuildFrom[List[A], B, That] bf)</pre>
3) Implicit parameters
Scala allows implicit parameters - let's lose that keyword:
<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">That ++ [B &gt;: A, That] (TraversableOnce[B] that, CanBuildFrom[List[A], B, That] bf)</pre>
4) Operators as method names
Scala allows operators as method names - let's be a bit more Java about it:
<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">That addAll [B &gt;: A, That] (TraversableOnce[B] that, CanBuildFrom[List[A], B, That] bf)</pre>
5) Generic Type Param position
Scala puts the generic type params between the method name and the parameter list, Java puts them first. Let's put it the Java way around:
<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">[B &gt;: A, That] That addAll(TraversableOnce[B] that, CanBuildFrom[List[A], B, That] bf)</pre>
6) Generic Type Param declaration
Scala uses [ and ] around its generic types, Java uses &lt; and &gt;. Let's go Java:

<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">&lt;B &gt;: A, That&gt; That addAll(TraversableOnce&lt;B&gt; that, CanBuildFrom&lt;List&lt;A&gt;, B, That&gt; bf)</pre>
7) Generic Type Param bounds declaration
Scala uses &gt;: and &lt;: where Java uses super and extends to set type bounds. Java version:
<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">&lt;B super A, That&gt; That addAll(TraversableOnce&lt;B&gt; that, CanBuildFrom&lt;List&lt;A&gt;, B, That&gt; bf)</pre>
We're now into a nearly valid Java signature for a method on a type List&lt;A&gt;. The only bit of this which does not compile is &lt;B super A&gt; - for reasons I don't fully understand Java does not support&nbsp; a lower bound for a type parameter on a method. Switch it to an upper bound however, and Java's perfectly happy:

<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">interface List&lt;A&gt; {
    &lt;B extends A, That&gt; That addAll(TraversableOnce&lt;B&gt; that, CanBuildFrom&lt;List&lt;A&gt;, B, That&gt; bf);
} </pre>
Conceptually upper and lower bounds are basically equivalent. So it's possible to express the type ideas in the example at the top almost entirely in pure Java.

Remember I'm focussed purely on the type system here - the method declaration we've ended up with is still complicated and has legibility issues with names. It's just that you can produce the same complications in Java - there's been no _added_ complexity from Scala. Here's a verbose version that might be easier to read:

<pre style="-webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px; background-color: #dddddd; border-bottom-color: rgb(119, 119, 119); border-bottom-style: solid; border-bottom-width: 1px; border-image: initial; border-left-color: rgb(119, 119, 119); border-left-style: solid; border-left-width: 1px; border-right-color: rgb(119, 119, 119); border-right-style: solid; border-right-width: 1px; border-top-color: rgb(119, 119, 119); border-top-style: solid; border-top-width: 1px; color: black; font-size: 11px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; orphans: 2; overflow-x: auto; overflow-y: auto; padding-bottom: 4px; padding-left: 8px; padding-right: 4px; padding-top: 4px; text-align: -webkit-auto; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px;">interface List&lt;E&gt; {
    &lt;SubE extends E, ReturnCollectionType&gt;
    ReturnCollectionType addAll(
            TraversableOnce&lt;SubE&gt; itemsToAdd,
            CollectionFactory&lt;List&lt;E&gt;, SubE, ReturnCollectionType&gt; factory);
}</pre>
The irony is that there are aspects of the Scala type system that _are_ significantly different to Java, and so are susceptible to accusations of a type system in overdrive. It's just that this method signature, used as an example of Scala's allegedly baroque type system, shows none of them.

Personally I find generic type parameters easier in Scala than Java - I don't know how much time I've wasted trying to work out how to make javac accept complicated generic signatures, with hopelessly illegible compile errors about "? capture 1 of 3"&nbsp; not matching. I have a suspicion that many of the complaints about the Scala type system either apply as much and often more to the Java type system, or are more a function of the vocabulary Scala developers use to describe the generic types being unfamiliar to Java developers who might otherwise recognise a concept familiar to them from Java.

For instance, to be told "List[A+] means List is covariant in A" is daunting. To find out that all this means is that effectively the declaration
val elements: List[Number] = new List(1, 2, 3)
&nbsp;in Scala is always equivalent to the declaration
List&lt;? extends Number&gt; elements = new ArrayList&lt;Integer&gt;();
&nbsp;in Java is much less daunting - Java developers are already aware that wildcards allow covariance and that as such List&lt;? extends Number&gt; is a valid supertype of List&lt;Integer&gt;. The Scala form is actually simpler &amp; more intuitive by not requiring the wildcard type bound on every reference &amp; instead declaring the variance rule on the type itself.