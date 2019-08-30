---
layout: post
title:  "Query EC2 instances from awscli"
date:   2019-08-30 15:49:00 +0200
categories: aws ec2 cli
---

## Background
I use ec2 instances in the geographical AWS region of my customer for latency testing etc. For launching the instances I have cli scripts with cloudinit configuration files. However, after launching, to quickly find the correct instance it comes in handy to query for a list with tags and public ip addresses. 

## AWS CLI command
```
aws ec2 describe-instances --filters "Name=key-name,Values=gerber_immunoscape" --query "Reservations[*].Instances[*].[InstanceId,PublicIpAddress,Tags[*]]"
```


