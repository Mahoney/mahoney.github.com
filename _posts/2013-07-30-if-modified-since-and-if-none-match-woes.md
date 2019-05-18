---
layout: post
title: If-Modified-Since and If-None-Match woes
date: '2013-07-30T14:32:00.001+01:00'
author: Robert Elliot
tags:
modified_time: '2013-07-30T15:09:13.312+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-5547961070333430286
blogger_orig_url: http://blog.lidalia.org.uk/2013/07/if-modified-since-and-if-none-match-woes.html
---

I'm confused about the correct behaviour of `If-Modified-Since` and
`If-None-Match` in certain scenarios.

The scenarios I have in mind are:

1. The client claims to have a later representation of a resource than an
   intermediate cache, but the intermediate cache's representation is still
   fresh.

   Imagine a cache has a fresh representation of `/resource` as so:
   ```http
   HTTP/1.1 200 OK
   ETag: some_etag
   Last-Modified: Fri, 26 Jul 2013 14:00:00 GMT
   ```

   It receives a request as so:
   ```http
   GET /resource HTTP/1.1
   If-None-Match: other_etag
   If-Modified-Since: Fri, 26 Jul 2013 14:00:01 GMT
   ```

   The nature of ETags is that they have no notion of sequence - it's either
   the same or it isn't, so the cache cannot know whether the ETags differ
   because one represents a later or earlier version of the resource. However,
   from my reading of the draft update to the HTTP/1.1 spec
   [HTTP Bis Section 5](http://tools.ietf.org/html/draft-ietf-httpbis-p4-conditional-23#section-5)
   the `If-None-Match` header takes precedence over `If-Modified-Since`, and so
   the correct behaviour here is for the cache to return a `200` with its old
   but fresh representation of the resource. That means that any client needs to
   be aware that it may quite legitimately receive an earlier version of an
   entity than the one it already has when making a conditional request to a
   cache. Which... disturbs me; my whole reason for making a conditional request
   is to find out if there's a fresher version, I don't want an older one!

   I suppose the theory is that the local client cache and the intermediate
   cache are obeying the same max-age / expires headers so by the time the
   client is needing to send conditional requests because its representation is
   stale the representation in the upstream cache should also be stale. However,
   this is not guaranteed - a request with a Cache-Control header forcing
   revalidation that goes via a different intermediate cache (due to load
   balancing) could easily legitimately leave a downstream client with a later
   representation than an upstream cache it later consults.

2. The client claims to have a later representation of a resource than an origin
   server.

   Imagine the entity is stored in a database.

   Origin node 1 has temporarily cached an old version of the entity with an
   `ETag` of `some_etag` and a `Last-Modified` of `Fri, 26 Jul 2013 14:00:00 GMT`.

   Origin node 2 meanwhile returns a later version of the entity to the client.

   The client then makes a conditional request which happens to get mapped to
   node 1:
   ```http
   GET /resource HTTP/1.1
   If-None-Match: other_etag
   If-Modified-Since: Fri, 26 Jul 2013 14:00:01 GMT
   ```

   What should node 1 return? It could:
   1. Treat this as a trigger to ensure that it updates its own cache.
   2. Assume that since `If-Modified-Since` is after its notion of
      `Last-Modified`, `304 Not Modified `is safe to return
   3. Assume the client is talking nonsense and simply return a `200` with its
      older version of the entity

   Again, [HTTP Bis Section 5](http://tools.ietf.org/html/draft-ietf-httpbis-p4-conditional-23#section-5)
   suggests that option 3 is the most "correct", though it feels very wrong to
   me. I favour option 1 followed by 3...

(One answer might be "don't use ETags if you care about this possibility". But
the spec is quite clear that origin servers SHOULD return an ETag if they can...)
