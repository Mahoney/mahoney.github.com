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

```java

interface FooInterface {
    void methodOnFooInterface();
}
interface BarInterface {
    void methodOnBarInterface();
}
class Foo implements FooInterface, BarInterface { ... }
class Bar implements FooInterface, BarInterface { ... }

```

Now imagine that you want to write a method that depends on taking items that are both FooInterface and BarInterface, but beyond that does not care about their types. It should accept both instances of Foo and Bar. In the past I'd have cursed the fact that Foo and Bar have no common supertype capturing being both FooInterface and BarInterface, but I now know you can declare a method as so:

```java

<T extends FooInterface & BarInterface> void doSomething(T instance) {
    instance.methodOnFooInterface();
    instance.methodOnBarInterface();
}
doSomething(new Foo());
doSomething(new Bar());

```

However, what I haven't yet worked out how to do is use a method that can take a Collection of that synthetic type, as so:

```java

<T extends FooInterface & BarInterface> void doSomethingElse(Collection<T> collection) {
    for (T instance : collection) {
        instance.methodOnFooInterface();
        instance.methodOnBarInterface();
    }
}

```

The method declaration works, but how to call it? I can find no way to instantiate a Collection with a synthetic type - this does not work:

```java

Collection<FooInterface & BarInterface> things = new ArrayList<FooInterface & BarInterface>); // DOES NOT COMPILE
this.add(new Foo());
this.add(new Bar());
doSomethingElse(things);

```

Can anyone help? This feels to me like a very powerful feature, potentially.

Edit: found a clunky way to do it:

```java

<T extends FooInterface & BarInterface> Collection<T> makeCollection(T instance1, T instance2) {
    Collection<T> collection = new ArrayList<T>();
    collection.add(instance1);
    collection.add(instance2);
    return collection;
}
doSomethingElse(makeCollection(new Foo(), new Bar()));

```

It would look more flexible with varargs, but then the compiler issues a warning about unchecked operations; a warning isn't the end of the world so that may be a preferable solution. But it's still pretty ugly - you cannot pull the `makeCollection` call out into an instance variable as there is no way to declare its type, so it has to stay inlined.
