---
layout: post
title:  "Setup ssh tunnel by port forwarding"
date:   2019-07-08 08:00:00 +0800
categories: ssh network port-forwarding
---

## Background
When a git repository (or any other service/application) dictates a port that is blocked on the local machine, while normal ssh connections are still allowed, it can come handy to setup an ssh tunnel through another machine.

## Possible Setup
In such cases, it is possible to make use of a virtual machine instance from a cloud platform such as AWS or Azure. A small standard instance can be created within seconds and allows port settings as needed.

In the below example, the local port `3333` is forwarded to the outside machine `13.229.157.1` that exist on AWS: 

```
ssh -L3333:git.eclipse.org:29418 ec2-user@13.229.157.1
```

This external machine sets up a tunnel from the local port `3333` to port `29418` on the machine `git.eclipse.org`. Now repos that are hosted on `git.eclipse.org` can be cloned by indicating port `3333`. On cloning, the port is stored in the repository configuration, hence in the future, the tunnel needs to be opened again. 


