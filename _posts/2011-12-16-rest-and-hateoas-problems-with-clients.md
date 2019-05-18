---
layout: post
title: REST and HATEOAS - Problems with Clients
date: '2011-12-16T11:44:00.000Z'
author: Robert Elliot
tags:
modified_time: '2011-12-16T11:44:17.658Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-5006677988217639440
blogger_orig_url: http://blog.lidalia.org.uk/2011/12/rest-and-hateoas-problems-with-clients.html
---

At Level 3 of the REST maturity model, the client is meant to bind to two things
- the entry URI for the system and the media type(s) of the entities sent and
received.

The client is then meant to retrieve any other URIs from links embedded in the
entities - links it can recognize by semantically meaningful rel attributes. In
theory this means that the providers of the service can then arbitrarily change
the URI patterns at which its resources reside, as the client can instantly
programatically react to this change.

I have a couple of concerns with this theory, which I'm going to explore in this
blog post.

* #### Programming such a client is more arduous
  Imagine that as a client there's some specific resource you want to
  retrieve -
  perhaps a representation of a customer. If you are obeying the principles of
  HATEOAS you go to the root URI, and retrieve the following resource:
  ```json
  {
    "link": {
      "rel":"customer",
      "href-pattern":"/customers/${customerName}"
    }
  }
  ```

  You then find the link with a rel of customer, get the href-pattern attribute,
  and use that to build the URI to the customer you want. Then you make sure you
  have configured a sensible HTTP cache for the root resource, otherwise that
  extra request to the root resource is an expensive overhead on every request
  for a customer.

  Or you behave badly, disobey HATEOAS and hard code the URI pattern:
  `/customers/${customerName}`

* #### URIs cannot be hidden from the client
  Many programming languages provide a means to enforce which elements are
  public, and hence suitable for clients to bind to, and which are not -
  normally with visibility modifiers of some form. There are frequently ways for
  clients to get round these restrictions, but at a bare minimum it makes it
  very clear to the client that they are not meant to be doing it and upgrades
  are likely to break them.

  There is no way to do this with a URI. If a badly behaved client decides to
  bind to some resource specific URI deep in your API, there's nothing to stop
  them and indeed nothing beyond documentation & knowledge of HATEOAS (which in
  my experience is still pretty limited in the general marketplace of software
  developers) to suggest to them that they are doing the wrong thing.

  In addition, unlike a programming library which is upgraded at the client's
  whim and so the client can do thorough tests to ensure the upgrade has
  broken nothing, a remote service may alter without the clients' knowledge; in
  this sense binding to a private API in a library is much safer than binding to
  a private API in a remote service.

  In practice this means that if the publishers of the service do decide to
  change the URI patterns they must do so in the knowledge that they may be
  breaking badly behaved clients; clients who may well not consider themselves
  to be behaving badly.

  There are circumstances where this may be acceptable. Those offering a free
  and desirable service to the general public may well take the attitude that if
  a client breaks because the client has not obeyed the HATEOAS contract, that's
  the client's problem. This is a nice situation to be in, but there are other
  circumstances in which it is more difficult to be so ruthless.

  If, as is common, the service is either an internal or external
  business-to-business service then in my experience if things stop working it's
  the people who changed something who are held responsible. Asserting that it
  is the client's fault for being insufficiently robust rarely cuts it with the
  top brass. The onus is generally on the people wishing to make a change to
  ensure it will not break their clients, rather than on clients to be robust.
  This is made particularly difficult as there is no way from the server side to
  tell whether a client is well behaved or not - they still hit the same URIs.
  Only actually looking at their code can show you whether or not you will break
  them before you do so. Alternatively, if the service is to the general public
  but requires payment the client is a paying customer, which makes their
  unhappiness a significantly bigger deal.

That combination of it being more arduous for your customer to create a HATEOAS
client and there being nothing to discourage them from binding to specific URI
patterns seems to me fairly toxic. In practice it seems to me that a real world
service is quite likely to have to treat its URIs as part of its public API.
