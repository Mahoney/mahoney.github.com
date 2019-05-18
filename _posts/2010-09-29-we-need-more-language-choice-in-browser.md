---
layout: post
title: We Need More Language Choice in the Browser
date: '2010-09-29T15:36:00.002+01:00'
author: Robert Elliot
tags: 
modified_time: '2010-09-29T16:01:10.735+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-7847846748232305555
blogger_orig_url: http://blog.lidalia.org.uk/2010/09/we-need-more-language-choice-in-browser.html
---

If you're a server-side developer, you have an enormous amount of freedom. You 
can write your code in Java, C++ JavaScript, Groovy, Scala, Ruby, Python, php, 
C# and probably a thousand other languages; fundamentally a web server will run 
as a stand-alone programme on hardware, and so as long as there's a compiler (or 
a virtual machine in the case of managed code) for your hardware you are good to 
go. You may get more or less help from libraries and frameworks, but in the end 
you are reading a stream of data from a socket and writing a stream of data 
back, and it's an odd language that can't do that.

 But the server side is dead. OK, that's a ridiculously massive exaggeration - 
 being sensible, clients are getting thicker and consequently servers are 
 getting thinner. And clients are getting thicker because of the increasing 
 power of browsers and the understanding that it's a lot simpler to handle 
 client state in the client than to manage it by passing it back and forth to 
 the server in multiple requests. Ajax, Web 2.0, Web Sockets, HTML5 - 
 increasingly the web is about delivering a thick client to the browser that 
 then only does server-side communication when it actually needs to get data or 
 persist data in a form that is non-local.

 All of which is lovely, but it's moved us from a world of massive choice when 
 creating business logic to a world of very little choice. Because browsers 
 don't run machine code, they run JavaScript, JavaScript and only JavaScript 
 (unless you want to lock yourself into Microsoft's cosy little ecosystem).

 Now I'm not saying JavaScript/ECMAScript is a bad thing. I suspect some notable 
 people think it is 
 [the Next Big Language](http://steve-yegge.blogspot.com/2007/02/next-big-language.html). 
 I'm sure you can write powerful, elegant and beautifully factored code in it.

 However, the joy of languages that run outside the browser is that if you 
 disagree with the way a language does things you can use a different one you 
 prefer. You don't need to have religious wars about it, there doesn't need to 
 be a One True Language To Rule Them All - you want to hack in Delphi, off you 
 go and hack in Delphi. That was why Apple's decision to limit the languages in 
 which you can develop for the iPhone was 
 [so wrong](http://enfranchisedmind.com/blog/posts/apple-is-just-microsoft-with-better-marketing/). 
 (You want to be employed by someone else to write their code for them and 
 you'll have to do it their way, but that's life as an employee.)

 We don't have that freedom on the browser. The browser doesn't even read 
 compiled code, it only reads source files. There are work-arounds, of course; 
 you can use [GWT](http://code.google.com/webtoolkit/) or 
 [XMLVM](http://www.xmlvm.org/overview/) or something similar to compile Java 
 (or Groovy or Scala...) to JavaScript. But this is not first class support for 
 another language; it's highly dependent on third party tools that may or may 
 not see much support (the XMLVM JavaScript output is no longer under 
 development).

 What I would like is if browsers could 
 [interpret JVM bytecode](http://ejohn.org/blog/running-java-in-javascript/), 
 not in an obscure Japanese framework that seems to have died, but natively in 
 the browser with the browser having the common runtime classes so they don't 
 have to be provided in every case. Then we could at least use Java, Groovy, 
 Scala, Jython, JRugby, Clojure and others to write our client side code. Of 
 course that's still a very JVM centric view of the world; I'm not really sure 
 how you could do this with languages like C and C++ that compile directly to 
 machine code and expect to manage memory themselves, but you could certainly 
 imagine doing it for the CLR byte code and so make all .net languages available 
 (IE probably does this, for all I know).

 There are challenges, of course; JavaScript as run in the browser is a single 
 event listening thread which cannot do I/O, so the available libraries would be 
 pretty limited and there would be a lot of standard runtime classes and 
 packages unavailable, just as they are in GWT. But it would be good to be freed 
 up again to allow developers to choose the language they prefer.

 (Am I revealing my lack of knowledge of Applets here? I think of them as 
 virtually stand-alone applications, rather than natively working with the 
 browser events, XMLHttpResponses and the structure of the DOM, but I've never 
 really used them in anger - I sort of assume that since GWT isn't applet based 
 they don't give you what I'm talking about, but I guess it's possible that 
 Google decided to go the GWT path to avoid needing the Java plugin on the 
 browser...)
