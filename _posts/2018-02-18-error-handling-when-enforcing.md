---
layout: post
title: Error handling when enforcing invariants in the construction of statically
  checked types
date: '2018-02-18T23:24:00.001Z'
author: Robert Elliot
tags:
modified_time: '2018-02-18T23:54:51.245Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-8205130463897428226
blogger_orig_url: http://blog.lidalia.org.uk/2018/02/error-handling-when-enforcing.html
---

tl;dr - error handling would be a lot less painful if the compiler could coerce
an `Either[A, B]` to an `A` or a `B` at compile time if the program would not
type check if it was an `Either[A, B]`, would type check if it was an `A` or a
`B`, and whether it was in fact a `Left[A, B]` or a `Right[A, B]` was decidable
at compile time.

Here's a problem that's been bothering me for a long time - how to error handle
when enforcing invariants in the construction of statically checked types. It's
the nature of types to constrain the set of values they can represent.
Consequently you have to construct an instance of a type from one or more other,
often more general, types, and in one form or another it will frequently
therefore be possible to call that constructor with values that are not in the
set of valid values for that type. At which point the attempt to construct that
instance of the type will hopefully be rejected, to prevent invalid program
state occurring. For instance, Java has a `URI` class with a constructor that
accepts a `String`. Since the `String` cannot be guaranteed to be a valid `URI`,
the constructor throws `URISyntaxException` if it's not a valid `URI`.

This introduces a problem, and one [I've written about before]({{ site.baseurl }}{% post_url 2010-01-29-checked-and-unchecked-exceptions %}).
Ideally in a statically type checked language the type checker will tell you
about the different values a function can return. And the `URI` constructor
function does just that, via the much hated checked exception; the constructor
will "return" either a valid `URI` or the exception, and the type checker will
force you to do something about it.

In more functional languages such as Scala or Haskell we might represent that
via a genuine return type - an `Either[URISyntaxException, URI]`, or a
`Try[URI]`, or perhaps `Option[URI]` if we're not interested in giving much
feedback on what went wrong. Which is all fine and dandy if the input is
untrusted. I like the type checker to remind me that if I take some input from a
user and try and turn it into a URI I need to handle the case when the user has
failed to oblige.

However, it becomes a lot more irritating in the case where we *know* the input
is valid. Whether it's because we're using the language to configure something,
or writing a test, or just jamming a little bit of code together to do something
right now that doesn't need to take inputs and validate them, there are times
when you just want to hardcode that value and so there's no error handling to be
done.

Let's say we define a type to represent the set of natural numbers:

```scala
object Natural {

  def apply(natural: Int): Either[IllegalArgumentException, Natural] =
    if (natural > 0) Right(new Natural(natural))
    else Left(new IllegalArgumentException(s"$natural is not a Natural number"))
}

case class Natural private (natural: Int) extends AnyVal
```
Cool - there's only one way to construct it, which enforces the invariant we
want, and the type checker tells us it may not succeed. And let's define a
function that will use it:

```scala
def sumOfFirst(n: Natural): Natural = ???
```

OK, let's test it:

```scala
test("sum of first 4 natural numbers") {
  sumOfFirst(Natural(4)) mustBe Natural(10)
}
```

And... compiler says no. I'm trying to pass an
`Either[IllegalArgumentException, Natural]` to a function that expects a plain
`Natural`, and I'm trying to compare the resulting plain `Natural` with an
`Either[IllegalArgumentException, Natural]`. Not going to work. But making the
compiler happy is going to make a bit of a mess of what was a fairly legible
test:

```scala
test("sum of first 4 natural numbers") {
  Natural(4).map(sumOfFirst) mustBe Natural(10)
}
```

So now you've got the additional cognitive load of mapping over an `Either` to
call the function you wanted to test, plus the fact that the types you are
actually comparing in the assertion are both
`Either[IllegalArgumentException, Natural]` rather than the simple `Natural` you
were interested in. Not nice.

In the URI case mentioned earlier, Java provides a get out of jail option:
```java
public static URI create(String str)
```
does not throw a checked exception, it will only fail at runtime if the string
is invalid. And we could do likewise for Natural. But that's a bit unsatisfying,
because in the process we've opened up the possibility that the programmer
abuses that facility, and the type checker has thereby lost some of its ability
to prove our programs correct.

Scala offers some interesting string parsing options where the compiler can
start statically checking the contents of a String to create an instance of a
constrained type and report any failure at compile time; Jon Pretty has made
this significantly easier with http://co.ntextu.al/.

However, it occurs to me that there might be a more general way of doing this.
What if, in cases where a pure function returns a result of type `Either[A, B]`,
but is being used in a way that means the compiler expects its result to be a
plain `A` or a plain `B`, and it is called in such a way that its arguments are
fully known at compile time, the compiler could actually call the function at
compile time and check whether it returns a `Left(a: A)` or a `Right(b: B)`? And
if it returns the correct one, permit its use directly as an `A` or `B`? And if
not, report the other as the text of a compile error?

This would then work entirely seamlessly, with no library code, and would not
require any String parsing for cases such as our `Natural` above that are not
naturally stringy, so long as your constructor function enforces its invariants
and reports the result as an `Either`. The first version of the test case above
would happily pass, as both the constructions of `Natural` could be proved at
compile time to return a `Right(n: Natural)` and hence be coerced to a `Natural`
since that would type check and the `Either[IllegalArgumentException, Natural]`
type would not.

The compiler would of course be clever enough to still treat the output as an
`Either[A, B]` if it were being used in a context that required it to be an
`Either[A, B]` - indeed, that would be the default. Only if the required type of
the output was not satisfied by the type `Either[A, B]` but _was_ satisfied by
either the type `A` or the type `B` would it bother to start trying to work out
whether it was decidable at compile time, and if so which it was.
