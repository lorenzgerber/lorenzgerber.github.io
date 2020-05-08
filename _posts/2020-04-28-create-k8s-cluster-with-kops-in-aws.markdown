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
export NODE_SIZE=${NODE_SIZE:-t3.micro}
export MASTER_SIZE=${MASTER_SIZE:-t3.micro}
export ZONES=${ZONES:-"eu-north-1a"}
export NAME=kube.cynodix.se
```

## Create the Kubernetes Cluster Configuration
The below command uses the ENV variables that have been set above.
```
kops create cluster $NAME \
--cloud aws \
--node-count 1 \
--zones $ZONES \
--node-size $NODE_SIZE \
--master-size $MASTER_SIZE
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

## Accessing the cluster from another machine
The whole kubernetes configuration is stored in the `~/.kube/config` file. Copying this file is enough to access the cluster with `kubectl`. To work with kops, the AWS kops profile for awscli need to be setup too. After a system restart, env variables need to be exported again eg. `AWS_PROFILE`, `NAME`,`KOPS_STATE_STORE`. 

## Accessing Kubernetes Web UI Dashboard
Deployment of the Kubernetes Web UI:  
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml`  
It can them be accessed through `kubectl proxy` on ` http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/`
However, to access a admin user needs to be created, `dashboard-adminuser.yml`:  
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
```
`kubectl apply -f dashboard-adminuser.yml`  

Then a cluster role binding needs to be created, `admin-role-binding.yml`:
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```
`kubectl apply -f admin-role-binding.yml`

Finally, the access token can be obtained: `kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')`



#### References
[Installing Kubernetes with kops](https://kubernetes.io/docs/setup/production-environment/tools/kops/)  
[kops - getting started on AWS](https://kops.sigs.k8s.io/getting_started/aws/)

