---
layout: post
title:  "Loging in to AWS ECR has changed from awscli v1 to v2"
date:   2020-03-09 07:00:00 +0200
categories: AWS Docker ECR
---

## Background
Login in to Amazon AWS ECR from the command line tool awscli has changed from v1 to v2. While loging in became more verbose in v2, it's also more general useable for multiple automation/scripting scenarios. 

## Version 1
Below version works provided that the region and credentials are configured in .aws
`$(aws ecr get-login --no-include-email)`

## Version 2
`aws ecr get-login-password | docker login --username AWS --password-stdin <your_aws_account_number_id>.dkr.ecr.<region>.amazonaws.com`

