---
layout: post
title: Injecting a bean into a Grails Controller without using auto-wiring
date: '2010-06-13T15:37:00.004+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-09-20T16:17:39.696+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-1840037548847826379
blogger_orig_url: http://blog.lidalia.org.uk/2010/06/injecting-bean-into-grails-controller.html
---

Imagine a nice simple Grails app - Foo1Controller and Foo2Controller both have a BarService property, auto injected by name as Grails likes to do.

<pre class="brush:groovy">
package foo 
class Foo1Controller { 
  BarService barService 

  def anAction = {
    barService.doSomething() 
  }
 }

package foo
class Foo2Controller { 
  BarService barService

   def anotherAction = {
     barService.doSomething()
   } 
}

BarService {
   void doSomeThing() {
     // do something 
  }
 } 
</pre>All is well, until we find that actually we need two different implementations of BarService, and the two controllers must each use a different one.

The initial refactor of the service is simple:

<pre class="brush:groovy">
interface BarService {
  void doSomething()
 }

   class Bar1Service implements BarService { 
  void doSomeThing() { 
    // do something
  }
}

class Bar2Service implements BarService {
  void doSomeThing() {
    // do something else
  }
}
</pre>Grails uses Spring, right?  So we can just choose to inject the right one into the right controller!  Problem is that Grails likes to auto-wire its controllers by name (or by type if you prefer).  Not so hot for a configuration &amp; server restart based change in implementation.  You're going to have to alter the source code to persuade Grails to wire Bar1Service into Foo1Controller and Bar2Service into Foo2Controller.

Well, turns out you can just do it in resources.groovy as so:

<pre class="brush:groovy">
barService(Bar1Service) { bean -&gt;
  bean.autowire = 'byName'
}

'foo.Foo2Controller'(Foo2Controller) { bean -&gt;
  bean.scope = 'prototype'
  bean.autowire = 'byName'
  barService = ref(ConfigurationHolder.config.foo2.barservice)
}
</pre>Config.groovy:

<pre class="brush:groovy">
foo2.barservice = 'bar2Service' // valid values are barService and bar2Service
</pre>Now, so long as your config is loaded from outside the war file, you can switch the implementation of BarService for Foo2Controller in config, restart and all is good - no need for a redeploy.

Interesting points:
<ul><li>By declaring Bar1Service with a name of barService in resources.groovy, we still got all the benefits of it being auto-wired into any controller (such as Foo1Controller) that declares a barService without needing to declare that controller in resources.groovy</li><li>By using bean.autowire = 'byName' in all the bean declarations we got the default Grails wiring for all other properties</li><li>When declaring the Controller in order to choose the BarService implementation we needed the full name - package.ClassName - as the bean name</li><li>When declaring the Controller we needed to give it a scope of prototype - to ensure we get a new instance of the Controller each time</li></ul>

One other thing - we're stuck on Grails 1.1.1.  So no guarantees this works with the latest version - though we are about to upgrade, so hopefully I'll be able to edit this post and remove this caveat soon.
