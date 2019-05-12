---
layout: post
title: CAP Theorem
date: '2013-10-23T21:46:00.003+01:00'
author: Robert Elliot
tags: 
modified_time: '2013-12-10T16:26:28.296Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-250730278401710114
blogger_orig_url: http://blog.lidalia.org.uk/2013/10/cap-theorem.html
---

<b>WARNING - on further reading I'm not at all sure the below is accurate. Take it with a large pinch of salt as part of my learning experience...</b>

I've been a bit confused over the meaning of the C, A and P of CAP theorem. I think I've got it sussed now, so this post is my attempt to encapsulate that knowledge and get it out there for someone to correct if I'm still wrong!
<h4>C - Consistent</h4>This is the easy one - I think i've always understood this, though I'm sure there are nuances to it. If you write some data then, on anyone anywhere trying to read it, then so long as they do not get an error or someone else has independently updated it in the meantime then they will see the same data you wrote.
<h4>A - Available</h4>Took me a while to get this one; I was thinking of it in terms of whether a system is up or not. That's not what Available means in this context. All it means is "able to return a response in a timely manner". That response could be as simple as a refusal to allow a new TCP connection - that's a response, and a timely one. An HTTP system returning 500 errors is available. If you're not timing out trying to communicate with the system, it's available, no matter how unhelpful the responses you are getting back are.

In contrast, a system is unavailable when a client gets nothing back at all and are left waiting until you timeout (you've got a timeout set up, right? Right?). Stick a Thread.sleep(Long.MAX_VALUE) in your HTTP handling code and your system is unavailable. Put a firewall in the way that quietly drops all response packets and you're unavailable.
<h4>P - Partition Tolerant</h4>There's two aspects to this one. The first is the obvious - a network partition occurs so that two nodes in a cluster are unable to communicate without them getting a chance to sign off from each other beforehand. What was less obvious to me at first is that a node that crashes is an example of a partition - not, as I naively thought, an example of being unavailable. The other nodes in the cluster cannot distinguish between "crashed" and "network issue somewhere between us".&nbsp;

A system is Partition Tolerant if it a) has more than one node and b) it can handle transactions without returning an error in the event that those nodes cannot communicate.&nbsp;
<h4>Consequences</h4>It should be obvious that network partitions can always happen wherever a cluster exists with multiple nodes that hold their own copy of state and that need to communicate over a network in order to maintain consistent state. CAP theorem says that when that partition happens, one of C, A and P has to be sacrificed. And now it should be fairly clear why. When a client attempts to write to a cluster which is partitioned, that write will arrive at one side or the other of the partition, and the system will have to do one of three things:
<h4>Wait for the Partition to Heal (CP)</h4>The simple solution to maintain consistency is for the node getting the write to wait, and not return a response until it knows that write has been committed on all nodes. Obviously this sacrifices availability - the partition may never heal, or not for a prohibitively long time. However, data will be consistent and no errors are returned, so we have a rather useless Partition Tolerance.
<h4>Discard the Write and return an Error (CA)</h4>Option two is to return an error. Consistency is maintained by the simple expedient of not changing&nbsp;state at all. The system is available - it's returning errors in a timely manner. However it's not partition tolerant - indeed it's questionable whether there's any benefit over a single node data store. By having more than one node and a network connection the chances of failure are simply increased. A single node data store is CA - it's either there or not.
<h4>Accept the Write (AP)</h4>The system is available and partition tolerant - no hanging, no error returned. The cost is that it is not consistent - the state either side of the partition is different, and someone reading from the other side of the partition will not see it. A dynamo style store with a read/write quota lower than half the nodes has sacrificed C in return for A and P.&nbsp;
<h4>It's Not That Simple</h4>Of course it isn't - the C, A and P qualities are not binary, they are a continuum, and data stores can make trade offs between them. A dynamo style store can choose to sacrifice some tolerance to a partition in return for more consistency by setting quora at a level of n/2 +1. A system could tolerate mild unavailability in the hope of the partition healing quickly. A store can vote up masters so that consistency is only sacrificed between partitioned halves, not sacrificed between all nodes.&nbsp;You get the idea.


