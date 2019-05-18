---
layout: post
title: Specifications, Tests & Code
date: '2013-02-19T13:55:00.002Z'
author: Robert Elliot
tags:
modified_time: '2013-02-19T13:55:36.671Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2176720518674447816
blogger_orig_url: http://blog.lidalia.org.uk/2013/02/specifications-tests-code.html
---

This is a quick reaction to various things I've read recently, most immediately
this tweet:
<blockquote class="twitter-tweet"><p>Test-First Programming comes across as so much less contentious/provocative/outré when you call it Spec-First Programming.</p>&mdash; Kevlin Henney (@KevlinHenney) <a href="https://twitter.com/KevlinHenney/status/303831637324619776">February 19, 2013</a></blockquote><script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

I think the observations
[in this article by Bertrand Meyer](http://cacm.acm.org/blogs/blog-cacm/156428-a-fundamental-duality-of-software-engineering/fulltext)
about the limits of testing are entirely correct. In any even vaguely complex
system you cannot begin to test all the combinations of inputs and outputs.
That's why we focus on testing what we think are the important cases and what we
think are the boundary conditions. I agree with him that as such the tests are
not the specification and cannot be. So I don't think we can just replace "Test"
with "Spec" and solve the problem.

(I should stick in a caveat here - I read a tweet by Ben Goldacre recently
saying that people who rebut tweets in blog posts (or newspaper articles) are
being prats, because a tweet by its nature is going to lack subtlety and depth
of argument. I dare say that Kevlin Henney would mount a staunch defence of what
he actually meant, perhaps along the lines of
[Martin Fowler's "Specification by Example" essay](http://martinfowler.com/bliki/SpecificationByExample.html)
which acknowledges that a specification by example will be necessarily
incomplete with the rest of the specification to be inferred from it.)

I think there's a useful analogy with real science. A specification is the
equivalent of a theory; F=MA, for instance, or E=mc2. A test is the equivalent
of an experiment; for a given set of controlled inputs, it measures the actual
output against that predicted by the theory. And the running system is the
equivalent of the real world. Just as in science, the tests (experiments) cannot
prove the specification (theory) holds in the runtime system (real world), they
can only disprove it. A black swan event can still occur (and anyone who has
ever written software will have encountered bugs in well tested software arising
from inputs the tester had not anticipated and so had not tested for).

The analogy breaks down in two respects; firstly, a correct but failing
experiment in science means that it's time to re-evaluate the theory, because
reality isn't subject to error, whereas often in programming it means that the
running system is not behaving as _actually_ desired.

Secondly, in science the theory (specification) is something a human being
writes and understands and is obviously distinctly separate from the real world
(runtime system); it may or may not accurately represent it. This leads me on to
the second article that prompted me to write this post;
[Leslie Lamport arguing that we need formal specifications in addition to code](http://www.wired.com/opinion/2013/01/code-bugs-programming-why-we-need-specs/).
To me a specification is a formal, logically precise, human readable statement
of precisely how a system is expected to operate under all conditions. So far so
in agreement. However, once you've got such a thing, I think it should be
possible to compile it into a form a computer can execute, and the name for
human readable text that can be compiled into a form that a computer can execute
is "source code".

I do not accept at all the the notion that the specification states "what and
why" and the code states "how". Code is written at multiple levels of
abstraction, typically represented by functions. I would argue that the why,
what and how are encoded in these abstraction layers. For any given function,
the function name states "what", the context of the parent function in which it
is called states "why" and the body of the function states "how". As you move up
and down the call stack, these roles change.

Which I think raises the question - if the runtime system (real world) is
actually compiled from the specification (the theory) and the tests
(experiments) are written to validate the specification (theory) is correct,
haven't we got a circular argument? How can the tests ever fail? And why do we
even need them?

I think the answer is that most of the time in programming we have two levels of
specification. One exists in our heads or in a requirement document or a user
story; it's informal, it doesn't cover all the cases, it may even be self
contradictory or downright impossible at times, but it's essentially "correct"
in the sense that it captures what we actually want this system to do. That's
the one we use to write our tests with. Then we have to create the formal
specification of what it should actually do under all circumstances, by writing
the code. Our tests are about validating that the formal specification actually
specifies what we were hoping it would specify.
