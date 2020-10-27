---
layout: post
title:  "Setup ssh tunnel by port forwarding"
date:   2019-07-08 08:00:00 +0800
categories: ssh network port-forwarding
---

## Background
When a git repository (or any other service/application) dictates a port that is blocked on the local machine, while normal ssh connections are still allowed, it can come handy to setup an ssh tunnel through another machine.

## Possible Setup
In such cases, it is possible to make use of a virtual machine instance from a cloud platform such as AWS or Azure. A small standard instance can be created within seconds and allows port settings as needed. The ssh server on this machine also needs to allow tunneling, which should be the default.

In the below example, the local port `3333` is forwarded to the outside machine `13.229.157.1` that exist on AWS: 

```
ssh -L3333:git.eclipse.org:29418 ec2-user@13.229.157.1
```

This external machine sets up a tunnel from the local port `3333` to port `29418` on the machine `git.eclipse.org`. Now repos that are hosted on `git.eclipse.org` can be cloned by indicating port `3333`, eg.:

```
git clone ssh://git@localhost:3333/example.git
```

 On cloning, the port is stored in the repository configuration, hence in the future, the tunnel needs to be opened again. 

## Another common usecase...
...is when the localhost of a remote machine should be reached. Actual example here was when trying out to setup shinyproxy on a remote linux server. Here also an ssh tunnel can be opened. Then the localhost of A.B.C.D can be reached in the local browser through `http:localhost:3000` 

```
ssh -L3333:localhost:8080 my_user@A.B.C.D
```

This post is loosely based on a [post](https://randyfay.com/content/git-over-ssh-tunnel-through-firewall-or-vpn) from Randy Fay.
