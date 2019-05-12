---
layout: post
title: Gradle Multi-Project Build with local SNAPSHOT dependency resolution
date: '2016-10-03T22:21:00.000+01:00'
author: Robert Elliot
tags: 
modified_time: '2016-10-22T12:24:34.212+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-1256262415238738788
blogger_orig_url: http://blog.lidalia.org.uk/2016/10/gradle-multi-project-build-with-local.html
---

<b>Note - 2016-10-22 - I missed <a href="https://docs.gradle.org/current/userguide/composite_builds.html">Gradle Composite Builds</a> which do something very similar</b>
<b>
</b>I often find myself on a project with multiple applications depending on common libraries, so I tend to end up with a super project looking like this:

<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">|</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">|-app1</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">|-app2</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">|-lib</span>

All three projects are separate git projects, separately built and deployed; the apps are on a continuous deployment pipeline, the lib requires a decision to cut a release to move it from a SNAPSHOT version to a fixed version. The top level project is just a convenience to allow checking them all out in one shot and building them all with one command.

During development of a feature that requires a change to the lib, I would update the dependency in the app that needs the feature to X.X.X-SNAPSHOT and work on them both at the same time.

In Maven this worked OK for development - both Maven and most IDEs would successfully resolve any SNAPSHOT dependencies locally if possible. Then after cutting a release of the app you only had to delete the -SNAPSHOT bit from the dependency version and job done.

However, Gradle does not do this by default; you have to specify the dependency as being part of your multi-module build as so:

<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">dependencies {</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">&nbsp; ...</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">&nbsp; compile project(':lib')</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">&nbsp; ...</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">}</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">
</span>
<span style="font-family: inherit;">This is much more invasive - changing to a release version of the lib now requires replacing that with:</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;"><br class="Apple-interchange-newline" />dependencies {</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">&nbsp; ...</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">&nbsp; compile 'mygroup:lib:1.2.3'</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">&nbsp; ...</span>
<span style="font-family: &quot;courier new&quot; , &quot;courier&quot; , monospace;">}</span>

<span style="font-family: inherit;">So you have to add the group of the lib, and know which precise version to specify, rather than just deleting '-SNAPSHOT' from the version. This makes it harder to automate changing the dependency - ideally, I would like to release the lib&nbsp;</span>automatically<span style="font-family: inherit;">&nbsp;as part of the continuous deployment process of the app after pushing a commit of the app which references a SNAPSHOT version of the lib.</span>
<span style="font-family: inherit;">
</span> <span style="font-family: inherit;">I'm experimenting with a way around this by manipulating the top level gradle build as so:</span>
<span style="font-family: inherit;">
</span>
<pre style="background-color: white; font-family: Menlo; font-size: 9pt;">subprojects.each { it.evaluate() }

<span style="color: #000043; font-weight: bold;">def </span>allDependencies = subprojects.collect { it.configurations.collect { it.dependencies }.flatten() }.flatten()
<span style="color: #000043; font-weight: bold;">def </span>localDependencies = allDependencies.findAll { dependency -&gt;
    subprojects.any { it.name == dependency.name &amp;&amp; it.version == dependency.version &amp;&amp; it.group == dependency.group }
}

subprojects {
    configurations.all {
        resolutionStrategy.dependencySubstitution {
            localDependencies.each {
                substitute module(<span style="color: green; font-weight: bold;">"</span>${it.group}<span style="color: green; font-weight: bold;">:</span>${it.name}<span style="color: green; font-weight: bold;">:</span>${it.version}<span style="color: green; font-weight: bold;">"</span>) with project(<span style="color: green; font-weight: bold;">":</span>${it.name}<span style="color: green; font-weight: bold;">"</span>)
            }
        }
    }
}</pre>
This effectively gives the Maven behaviour - and IntelliJ at least respects it correctly and resolves the dependencies to the same workspace

You can play with an example here:
<a href="https://github.com/lidalia-example-project/parent">https://github.com/lidalia-example-project/parent</a>