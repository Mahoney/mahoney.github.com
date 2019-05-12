---
layout: post
title: Apache HTTPD - settings for use as a pure proxy
date: '2011-11-07T14:33:00.001Z'
author: Robert Elliot
tags: 
modified_time: '2011-12-16T10:32:04.791Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2424792584345651324
blogger_orig_url: http://blog.lidalia.org.uk/2011/11/apache-httpd-settings-for-use-as-pure.html
---

When using Apache HTTPD as a pure proxy to an application server, it may be useful to set AllowEncodedSlashes to "On" and set nocanon on the ProxyPass. This has the effect that the URIs are passed to the application server "as is" without Apache doing any security check on them or otherwise attempting to correct them. Naturally this puts the onus on the origin server to be secure.

It may also be useful to set retry=0. By default after a failure to get a response from the origin server HTTPD caches the fact that the origin server is unavailable for a minute. This is a pain when automating deployments. Setting retry=0 makes it genuinely proxy every call down to the origin server regardless.

 <pre>
    AllowEncodedSlashes On
    ProxyPreserveHost On
    ProxyPass / http://localhost:8080/ retry=0 nocanon
    ProxyPassReverse / http://localhost:8080/
</pre> Along the same lines, when using Tomcat to serve RESTful requests it may be useful to allow encoded slashes. This is turned off by default because if you have a servlet that serves up files it may allow an attacker to retrieve arbitrary files from your server using ../../ type paths. If you are mapping all URLs to servlet(s) that do not do this then you can re-enable them using the following command line arguments: <pre>
-Dorg.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=true
-Dorg.apache.catalina.connector.CoyoteAdapter.ALLOW_BACKSLASH=true
</pre>or by adding the following to $CATALINA_HOME/conf/catalina.properties: <pre>
org.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=true
org.apache.catalina.connector.CoyoteAdapter.ALLOW_BACKSLASH=true
</pre>

