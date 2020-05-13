---
layout: post
title:  "HTTPS terminated shinyproxy on EKS Kubernetes Cluster"
date:   2020-05-12 07:00:00 +0200
categories: EKS Shinyproxy K8s Kubernetes AWS
---

## Background
[Shinyproxy](https://shinyproxy.io) is a backend to serve R Shiny applications dockerized in a scalable way. This blog post is a loose instruction/cheatsheet on how to setup a K8S cluster using EKS on AWS, then configure and run Shinyproxy on it.

## Install tools
`export AWS_PROFILE=cynodix`
eksctrl  
aws-iam-authenticator  
kubernetes-cli  

## Launch Cluster
eksctl  

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


