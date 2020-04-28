---
layout: post
title:  "Create K8s cluster with kops in AWS"
date:   2020-04-28 07:00:00 +0200
categories: K8s Kubernetes AWS kops
---

## Background
Kops allows setting up Kubernetes. This is ment as a short checklist to configure and launch K8s cluster in AWS. Basically only an extraction from the already excellent documentations available at the respecitive github projects.

## Summary how the environment was prepared
The work machine in my case is on OSX. `kubectl` and `kops` were installed using Homebrew. In some cases, the links for `kubectl` could not be created on installation due to `kubectl` already being available through the docker installation.

My domain name is registred with a local company but administrated on AWS Route53. As suggested in the kops documentation, a second 'Hosted Zone' was creatded for the subdomain to be used with the kubernetes cluster. Further, a S3 bucket had to be configured as state storage for the cluster. Against recommendation, it was created in my 'local' aws region instead of US. S3 default bucket encryption and versioning was applied to the bucket.

## Setting ENV variables
In my case awscli is configured with multiple profiles. Hence the profile to be used by default can be set as ENV variable:

`export AWS_PROFILE=profile_name`

Then a number of configuration values are made accessible as ENV variables.
```
export KOPS_STATE_STORE="s3://kube-cynodix-se-state-store"
export NODE_SIZE=${NODE_SIZE:-t3.medium}
export MASTER_SIZE=${MASTER_SIZE:-t3.medium}
export ZONES=${ZONES:-"eu-north-1a"}
```

## Create the Kubernetes Cluster Configuration
The below command uses the ENV variables that have been set above.
```
kops create cluster kube.cynodix.se \
--node-count 3 \
--zones $ZONES \
--node-size $NODE_SIZE \
--master-size $MASTER_SIZE \
```

## Use Cluster Configuration to launch Kubernetes Cluster into AWS
`kops update cluster kube.cynodix.se --yes`

## Some commands to check/use the cluster
```
kops validate cluster
kops get cluster
kubectl get nodes
```

## To shutdown and delete the cluster
`kops delete cluster kube.cynodix.se --yes`


#### References
[Installing Kubernetes with kops](https://kubernetes.io/docs/setup/production-environment/tools/kops/)
[kops - getting started on AWS](https://kops.sigs.k8s.io/getting_started/aws/)

