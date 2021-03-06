---
layout: post
title: Stupid Stuff I did Implementing an Atom Event Feed Service & Client
date: '2013-07-24T18:22:00.001+01:00'
author: Robert Elliot
tags:
modified_time: '2013-07-24T18:24:18.298+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2150053529937798921
blogger_orig_url: http://blog.lidalia.org.uk/2013/07/stupid-stuff-i-did-implementing-atom.html
---

Some quick notes on the issues we've had implementing an
[Atom Feed](http://tools.ietf.org/html/rfc4287) as a means of exposing events to
clients over HTTP as suggested by
[REST in Practice](http://restinpractice.com/book/). You can read up on the full
details
[in the book here](http://answers.oreilly.com/topic/2153-rest-in-practice-how-to-use-atom-for-event-driven-systems/).

#### Decisions I'm happy with:

1. We implemented the feeds using [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)
   formatted query params:
   `http://hostname/eventfeed/period?from=2013-07-24T09:30Z&to=2013-07-24T09:40Z`.
   This worked well, as it meant we could as humans quickly find specific events
   we were interested in - in dev environments we have far fewer events, so we
   can simply ask for all those in a day rather than searching for a ten minute
   period that has one.

2. We made the "from" inclusive and the "to" exclusive, to ensure that an entry
   only ever appears in one feed, and also ensure that if we set the
   `Last-Modified` header to the latest entry in the working feed and the to
   date on an archive feed the `Last-Modified` header on a feed when it is
   archived is guaranteed to be later than the `Last-Modified` header on the
   feed when it was the working feed, even if the last event occurred a
   millisecond before the feed was archived.

3. The persistent atom feed client we've built has proven very stable - all of
   the bugs have been on the server side (mostly around cache headers), proving
   that so long as the atom and http specs are followed rigorously on the server
   side writing a client is trivial.

4. We've used the client in a replayable way - processing an entry simply
   updates our local cache of the entity to the _current state_ of the entity by
   following a link. Whilst events should not arrive out of order or ever be
   replayed, it doesn't (functionally!) matter if they are.

#### Mistakes I made:

1. ##### Calculating whether a feed is a working feed or not multiple times when responding to the same request.

   This one is embarrassing, as it's a real schoolboy error, but I made it so
   may as well 'fess up. The same URI will at first point to a working feed and
   later point to an archive feed based on whether its period includes the
   current time or is entirely in the past. The working feed and the archive
   feed differ in their cache characteristics (the archived feed does not
   change, and so can be cached for a very long time) and also in whether or not
   they have a "next" link - an archive feed will, a working feed will not.
   Calculating whether or not a request was for a working feed twice in the same
   request can result in building the entity as a working feed, but setting the
   cache headers for an archive feed - which is disastrous, as it may lack
   events and will lack a next link.

2. ##### Using the same Etag & Last-Modified values for working and archived feeds

   We went for the Etag of a feed being the entry ID of the latest entry in the
   feed, but this created an issue - the `Etag` for the working feed must be
   different to the `Etag` for an archived feed, otherwise a conditional
   (`If-None-Match`) request for a feed that _was_ the working feed but has
   since become an archive feed will get a `304 Not Modified` response, with an
   eternal cache header since archived feeds should never change. Since the
   working feed will not have a "next" link, this means that the feed client
   gets stuck with a cached archive feed with no "next" link, so as it walks
   forward through the feed it always stops on that archive under the impression
   that there is no later feed whose events it can consume.

   Solving this was relatively trivial - we just appended "-archive" or
   "-working" to the Etag depending on which the feed was.

   In the same way the Last-Modified header should be the "updated" date of the
   latest entry on a working feed, but should be the "to" date on an archived
   feed to ensure that when a feed moves from being a working feed to being an
   archived feed a conditional `If-Modified-Since` request from the client will
   not result in the server returning a `304 Not Modified response` to the
   client.

3. ##### Caching the latest feed independent of the working feed.

   The client starts at the latest feed, which is a static URI which we allow to
   be cached for 1 second. It works backwards following the previous links in
   the feeds until it finds the feed containing the latest entry it has
   processed. It then works forward processing each entry and following the next
   links in the feed to find the next feed. This ultimately leaves it at the
   working feed (not the latest feed - the feed prior to the working feed has
   been archived, so in order for it to be cached eternally it must link to the
   working feed so the link is still correct when the working feed is itself
   archived). The working feed is also cached for 1 second.

   The danger here is that because the latest and working feeds are cached
   separately, they may have different values in the cache - on run one, the
   latest feed is cached without event x, but by the time the working feed is
   requested it contains event x. This is then stored as the latest handled
   event. On run two, the cached latest feed may be returned - which does not
   contain event x, so the client follows the previous link chain to the
   earliest archived feed and in the process replays all of the events that it
   has already handled.

   Due to our decision to make the events replayable this is not functionally
   catastrophic, but obviously it creates performance and latency issues.

   There are three solutions we have thought of to this:

   1. Stop allowing the responses on the latest and working feeds to be cached
      at all. This is undesirable, as it removes the scalability characteristics
      we get from using even very limited (`max-age=1`) generic HTTP caching.

   2. Add a [Content-Location](http://tools.ietf.org/html/rfc2616#section-14.14)
      header to the latest feed, pointing at the working feed, and to the
      working feed, pointing at the latest feed. As I understand it this is
      simply a hint to any cache that they are the same thing at the moment the
      response is received (crucially not thereafter), and therefore that the
      cache may update its representations under other URIs appropriately. A
      quick test suggested this worked with the Apache HTTP Client cache
      implementation, but the HTTP 1.1 spec itself does not go so far as to
      promise all caches will honour it, and it seems a relatively little talked
      about part of the spec. I'm not entirely sure that my reading of the spec
      is correct, and clearly there is a lot of potential room for bugs in
      caches which would affect us in a case such as this where the relationship
      between the URI of the latest feed and the URI of the working feed is only
      ever a temporary one.

   3. Switch the latest response from a `200` containing the feed to a `307` or
      `302` redirect to the working feed. This response can in turn be cached
      using an `Expires` header for the remainder of the working feed period.
      This seems relatively simple, so we're going to trial this approach.

4. ##### Using ROME/JDom with Xerces, and XML as the entries' content's type
   We used ROME for both creating and consuming our ATOM feed, but if the
   content type of the content element of your entry is XML then ROME runs up
   a new SAXBuilder in JDom, to parse it and prove it is valid. This in turn
   does a lot of reflection based looking up of factories, and because we're
   using Apache TomEE it ends up with Xerces 2.9. This process happens for every
   single entry in every single feed, and for us proved catastrophically
   expensive - with just two clients loading up feeds concurrently we ended up
   with the service essentially becoming unavailable.

#### Things that worry me:

At present we are storing the events in an Oracle database using a column
defined as `TIMESTAMP(3) WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP`. I'm a little
concerned that if the time on the database server were ever set backwards
(perhaps due to an NTP correction) it is possible a new event would be inserted
_prior_ to events that clients had already consumed, and so those clients would
never see it. It's possible that we could do the insert rather more
intelligently - something along the lines of CURRENT_TIMESTAMP or
latest event + 1 millisecond, whichever is the later, to ensure that a new event
always happens after the latest event; though I need to play with some SQL to
have a clear idea of whether this is possible.

Another option might be to attempt to maintain a sequence and return events
sequentially rather than by time; however, this then presents us with the issue
of ensuring the sequence is guaranteed to be sequential, which might not be the
case with Oracle RAC and would be very hard should we have to distribute the
database in a more fundamental way. I would be interested to hear of other
people's solutions to this issue.

#### Still To Do:

I'd really like to open source both the client and server code for this, as the
whole point is that it's a generic mechanism that can be used domain
independently. The server side code, tied as it is to containers such as Spring
and the Servlet API, is perhaps less widely useful; but the client side code is
a pure library.

I'd also like to explore
[atom-archive-traverser](https://github.com/plasma147/atom-archive-traverser),
which I've just found and looks very similar to our client though built on
different libraries.
