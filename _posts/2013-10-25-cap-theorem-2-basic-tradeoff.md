---
layout: post
title: CAP Theorem 2 - The Basic Tradeoff
date: '2013-10-25T09:30:00.001+01:00'
author: Robert Elliot
tags:
modified_time: '2013-12-10T16:27:06.568Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-1151440059147209511
blogger_orig_url: http://blog.lidalia.org.uk/2013/10/cap-theorem-2-basic-tradeoff.html
---

*WARNING - on further reading I'm not at all sure the below is accurate. Take it
with a large pinch of salt as part of my learning experience...*

### tl;dr:

You can't sacrifice Availability, so you have to choose between being Consistent
and being Partition Tolerant. But only in the event of a network partition! You
can be Partition Tolerant and still be Consistent when no partition is
occurring.

Following up from
[my previous post on CAP Theorem]({{ site.baseurl }}{% post_url 2013-10-23-cap-theorem %}),
I'm going to discuss what in practical terms the CAP trade-off means.

#### A is non-negotiable - a truly CP data store is a broken idea

Remember, "Available" doesn't mean "working", "Available" means "doesn't hang
indefinitely". In the event of a network partition a truly CP data store will
simply hang on a request until it has heard from all its replicas. A system that
hangs indefinitely is a chocolate tea pot. Poorly written clients will also hang
indefinitely, ending up with users sitting staring at some equivalent of the
Microsoft sand timer. In the end someone (a well written client, or just the
poor schmuck staring at his non-responsive computer) will decide to time the
operation out and give up, leaving them in the same not-working state as a CA
system but with the additional worry that they've no idea what happened to the
request they sent.

### Hang on, there are CP data stores out there aren't there?

No, not really - not as I understand CAP theorem, anyway. See below!

#### The choice is between CA and AP

In fact it can be reduced to a very, very simple trade-off - in the event of a
network partition, do I want the data store to continue to work or do I want the
data store to remain consistent?

#### CA means a single point of failure

CA is the simplest model. It's what we get when we run up a single node ACID
data store - it's either there, working and consistent or it isn't. There are
ways to add a measure of redundancy to it in the form of read-only slaves with a
distributed lock, but fundamentally if a network partition occurs between them
and the master then the master has to stop accepting writes if it is to remain
consistent with the slave.

It's a model that means outages are essentially guaranteed. If that's acceptable
then it's nice and easy for developers to work with; but it's rarely acceptable.

#### Which leaves AP

Nearly all data stores used in scenarios where there is a desire to avoid
outages entirely in so far as is possible (human error notwithstanding). Which
means having multiple copies of state on machines connected by the network,
which means network partitions can and will happen. Which means needing to be
available and tolerant of those partitions.

### Oh noes! No consistency! Sounds dreadful...

The important point to remember here is that the loss of Consistency implied by
Partition Tolerance (i.e. Continuing to Work) _only has to be accepted in the
event of a partition_. This is what lots of so-called "CP" systems are trying to
do - remain consistent whilst the network is healthy, and only become
inconsistent in the event of a partition.
