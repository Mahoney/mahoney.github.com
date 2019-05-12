---
layout: post
title: Fixing Duplicate Resource Definitions for Defaulted Parameterised Defines in
  Puppet
date: '2013-12-30T14:08:00.002Z'
author: Robert Elliot
tags: 
modified_time: '2013-12-30T14:08:30.792Z'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-9154987933106320879
blogger_orig_url: http://blog.lidalia.org.uk/2013/12/fixing-duplicate-resource-definitions.html
---

Recently I have been working on a puppet module which defines a new resource which in turn requires a certain directory to exist, as so:

<pre title="mything/init.pp">define mything ($log_dir='/var/log/mythings') {

  notify { "${name} installed!": }

  file { $log_dir:
    ensure => directory
  }

  file { "${log_dir}/${name}":
    ensure => directory,
    require => File[$log_dir]
  }
}
</pre>
As you can see the log directory is parameterised with a default, combining flexibility with ease of use.

As it happens there's no reason why multiple of these mythings shouldn't be installed on the same host, as so:

<pre>mything { "thing1": }
mything { "thing2": }
</pre>
But of course that causes puppet to bomb out:
Duplicate definition: File[/var/log/mythings] is already defined

The solution I've found is to realise a virtual resource defined in an unparameterised class, as so:

<pre title="mything/init.pp">define mything ($log_dir='/var/log/mythings') {

  notify { "${name} installed!": }

  include mything::defaultlogging

  File <| title == $log_dir |>

  file { "${log_dir}/${name}":
    ensure => directory,
    require => File[$log_dir]
  }
}

</pre><pre title="mything/defaultlogging.pp">class mything::defaultlogging {
  @file { '/var/log/mythings':
    ensure => directory
  }
}
</pre>
Now the following works:
<pre>mything { "thing1": }
mything { "thing2": }
</pre>
If we want to override and use a different log directory as follows:
<pre>mything { "thing3":
  log_dir => '/var/log/otherthing'
}
</pre>we get this error:
Could not find dependency File[/var/log/otherthing] for File[/var/log/otherthing/thing3] at /etc/puppet/modules/mything/manifests/init.pp:12

This just means we need to define the new log directory as so:
<pre>$other_log_dir='/var/log/otherthing'
@file { $other_log_dir:
  ensure => directory
}
mything { "thing3":
  log_dir => $other_log_dir
}
</pre>and all is good. Importantly, applying this manifest will <b>not</b> create the default /var/log/mythings directory.