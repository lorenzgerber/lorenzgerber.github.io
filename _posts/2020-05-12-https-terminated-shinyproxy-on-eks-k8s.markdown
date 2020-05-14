---
layout: post
title:  "HTTPS terminated shinyproxy on EKS Kubernetes Cluster"
date:   2020-05-12 07:00:00 +0200
categories: EKS Shinyproxy K8s Kubernetes AWS
---

## Background
[Shinyproxy](https://shinyproxy.io) is a backend to serve R Shiny applications dockerized in a scalable way. This blog post is a loose instruction/cheatsheet on how to setup a K8S cluster using EKS on AWS, then configure and run Shinyproxy on it.

### Cost considerations
It's not cheap  

## Install tools
### eksctrl  
At the time of this writing, the aws [Getting started with eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) mentioned to use a version of at least `0.19`. However installation through `Homebrew` resulted in a lower version, hence a compiled version was downloaded from the [eksctl github page](https://github.com/weaveworks/eksctl). The `tar.gz` file was extracted and moved to `/usr/local/bin/`.

### Homebrew installs
Both `aws-iam-authenticator` and `kubernetes-cli` can be installed using Homebrew.  

## Create EKS Cluster

### Create and Launch EKS Cluster
For successful installation, enough EIP need to be available for the account in use in the current region. And don't forget to choose the correct AWS Profile for `awscli`:    
`export AWS_PROFILE=cynodix`  

Below follows a possible example of how a cluster to be created using `eksctl`:  
```
eksctl create cluster --name shiny \
--region eu-north-1 \
--nodegroup-name shiny-workers \
--node-type t3.medium \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--ssh-access \
--ssh-public-key cynodix_aws.pub \
--managed
```

This may take a while. After installation, the cluster is configured for `kubectl` access. If needed, configuration can be transfered to other machines by copying the config from the `.kube` directory. 

### Deploy the web UI
[Deployment of the web UI](https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html) is the same as described in an earlier blogpost, however a different service account name is used here (`eks-admin`).

## Prepare Shinyproxy
obtain ACM  
configure  
build images  
upload images  

## Launch Shinyproxy on K8s
kubectl create deployment  
kubectl create authentication  
kubectl create service  

## Configure DNS in Route53
make alias in Route53 hosted zone  

## Test




#### References


