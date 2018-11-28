---
layout: post
title:  "Github diffing a File over two Branches"
date:   2018-11-11 09:34:28 +0800
categories: github diff
---

A fairly common situation when working on a feature branch is the need to compare it to
the master branch. This can be easily done by:
```
git diff one_branch..master -- fileOfInterest.js
```

The `--` separator indicates the end of the command line flags.