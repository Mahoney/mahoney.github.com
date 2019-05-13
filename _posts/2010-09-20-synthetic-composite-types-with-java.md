---
layout: post
title: Synthetic Composite Types with Java Generics
date: '2010-09-20T15:05:00.007+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-09-29T15:59:08.946+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-1401499193318811911
blogger_orig_url: http://blog.lidalia.org.uk/2010/09/synthetic-composite-types-with-java.html
---

Perhaps everyone already knows about this, but I just found a very interesting feature of Java Generics - you can declare synthetic composite types using multiple bounds to allow you to treat two different classes that implement the same interfaces as if they had a common super type, even if they don't.

Consider the following classes:

<pre class="brush:java">
interface FooInterface {
    void methodOnFooInterface();
}
interface BarInterface {
    void methodOnBarInterface();
}
class Foo implements FooInterface, BarInterface { ... }
class Bar implements FooInterface, BarInterface { ... }
</pre>Now imagine that you want to write a method that depends on taking items that are both FooInterface and BarInterface, but beyond that does not care about their types. It should accept both instances of Foo and Bar. In the past I'd have cursed the fact that Foo and Bar have no common supertype capturing being both FooInterface and BarInterface, but I now know you can declare a method as so:

<pre class="brush:java">
&lt;T extends FooInterface &amp; BarInterface&gt; void doSomething(T instance) {
    instance.methodOnFooInterface();
    instance.methodOnBarInterface();
}
doSomething(new Foo());
doSomething(new Bar());
</pre>However, what I haven't yet worked out how to do is use a method that can take a Collection of that synthetic type, as so:

<pre class="brush:java">
&lt;T extends FooInterface &amp; BarInterface&gt; void doSomethingElse(Collection&lt;T&gt; collection) {
    for (T instance : collection) {
        instance.methodOnFooInterface();
        instance.methodOnBarInterface();
    }
}
</pre>The method declaration works, but how to call it? I can find no way to instantiate a Collection with a synthetic type - this does not work:

<pre class="brush:java">
Collection&lt;FooInterface &amp; BarInterface&gt; things = new ArrayList&lt;FooInterface &amp; BarInterface&gt;); // DOES NOT COMPILE
this.add(new Foo());
this.add(new Bar());
doSomethingElse(things);
</pre>Can anyone help? This feels to me like a very powerful feature, potentially.

Edit: found a clunky way to do it:

<pre class="brush:java">
&lt;T extends FooInterface &amp; BarInterface&gt; Collection&lt;T&gt; makeCollection(T instance1, T instance2) {
    Collection&lt;T&gt; collection = new ArrayList&lt;T&gt;();
    collection.add(instance1);
    collection.add(instance2);
    return collection;
}
doSomethingElse(makeCollection(new Foo(), new Bar()));
</pre>It would look more flexible with varargs, but then the compiler issues a warning about unchecked operations; a warning isn't the end of the world so that may be a preferable solution. But it's still pretty ugly - you cannot pull the <pre>makeCollection</pre> call out into an instance variable as there is no way to declare its type, so it has to stay inlined.
