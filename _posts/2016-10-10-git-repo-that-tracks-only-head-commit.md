---
layout: post
title: Git repo that tracks only the head commit of a branch
date: '2016-10-10T22:51:00.000+01:00'
author: Robert Elliot
tags: 
modified_time: '2016-10-10T22:51:27.823+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-3546847481269234362
blogger_orig_url: http://blog.lidalia.org.uk/2016/10/git-repo-that-tracks-only-head-commit.html
---

Quick note to self on how to have a git repo that only contains a single commit, the head of a branch.

Initiate it with:

`git clone <url> --branch <branch_name> --depth=1`

Update it with:

`git pull && git pull --depth=1 && git reflog expire --expire-unreachable=now --all`

