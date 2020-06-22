---
layout: post
title:  "Add AWS Cloud Build Setup through CLI"
date:   2020-06-22 07:00:00 +0200
categories: devops AWS codebuild codepipeline cloudwatch events cloudtrail
---

## Background
In a project that uses AWS Devops products CodeBuild and CodePipeline to automate continuous delivery, it bugs me every time when I have to setup a pipeline for a new functionality. Funcitionalities here are analytical pipelines, mostly using R code, packaged in docker containers to be run as batch jobs on AWS Batch. Each such functionality is build in two steps, first as package (which is then stored as archieve on S3) and as a docker image on ECR. There exists for each such functionality a develop and production build pipeline. Setting up the devops pipelines for a new funcitonality using the aws console feels awkward and prone to missconfiguration, so I was looking how this could be streamlined using only config files and the awscli. Also, using the console will for each project generate new roles and policies etc. while the needed roles could usually be setup in a reusable way to prevent bloating the account with near identical configuration files.

## Preparation
Basically, the setup involves the following steps:
1. Setup CodeBuild project for R package build
2. Setup CodeBuild project for Docker Image build
3. Setup CodePipeline for R package build
4. Setup CodePipeline for Docker build

However, to generate a new codebuild/codepipeline project, rolea with apropriate policies attached are needed. Hence, before being able to script generation of new projects, the needed generic policies and roles have to be created.

### Create CodeBuild Policy
`aws iam create-policy --policy-name codebuild-generic-devops-policy --path /service-role/ --policy-document file://code_build_policy_statement.json`  
The `code_build_policy_statemtn.json` for generic access needed by code-build:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Resource": [
                "arn:aws:logs:ap-southeast-1:781462664882:log-group:/aws/codebuild/*",
                "arn:aws:logs:ap-southeast-1:781462664882:log-group:/aws/codebuild/*:*"
            ],
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ]
        },
        {
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::codepipeline-ap-southeast-1-*"
            ],
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:GetBucketAcl",
                "s3:GetBucketLocation"
            ]
        },
        {
            "Effect": "Allow",
            "Resource": [
                "arn:aws:codecommit:ap-southeast-1:781462664882:*"
            ],
            "Action": [
                "codecommit:GitPull"
            ]
        },
        {
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::codepipeline-ap-southeast-1-254100543197",
                "arn:aws:s3:::codepipeline-ap-southeast-1-254100543197/*"
            ],
            "Action": [
                "s3:PutObject",
                "s3:GetBucketAcl",
                "s3:GetBucketLocation"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "codebuild:CreateReportGroup",
                "codebuild:CreateReport",
                "codebuild:UpdateReport",
                "codebuild:BatchPutTestCases"
            ],
            "Resource": [
                "arn:aws:codebuild:ap-southeast-1:781462664882:report-group/*"
            ]
        },
	{
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::codepipeline-ap-southeast-1-254100543197/*",
                "arn:aws:s3:::codepipeline-ap-southeast-1-254100543197/*/*.zip/*"
            ]
        },
        {
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::codepipeline-ap-southeast-1-254100543197"
            ],
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketAcl",
                "s3:GetBucketLocation"
            ]
        },
	{
            "Effect": "Allow",
            "Action": [
                "ecr:*",
                "cloudtrail:LookupEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

### Create CodeBuild Role
To create the Role, an `assume-role-policy-document.json` is needed:  
```
{
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "Service": "codebuild.amazonaws.com"
          },
          "Action": "sts:AssumeRole"
        }
      ]
}
```
Then the role can be created using:  
`aws iam create-role --role-name codebuild-generic-devops-role --path /service-role/ --assume-role-policy-document file://code_build_assume_role_policy_document.json`

Then the policy has to be attached to the role:  
`aws iam attach-role-policy --role-name codebuild-generic-devops-role --policy-arn arn:aws:iam::781462664882:policy/service-role/codebuild-generic-policy-devops`

### Create CloudWatch Policy

### Create CloudWatch Role

### Attach Policy to Role




## Integrate new R Package into cloud build pipeline

### Add CodeBuild Package
done
### Add CodeBuild Docker
done
### Add CodePipeline Package
done
### Add CodePipeline Docker
done
### Put Event Rule Package (git)
todo
### Put Event Target Package (git)
todo
### Put Event Rule Docker (S3)
`aws events put-rule --name devops-processing-prod-s3-rule --cli-input-json file://events_rule_pipeline_s3_template.json`
### Put Event Target Docker (S3)
`aws events put-targets --rule devops-processing-prod-s3-rule --targets Id=processing-docker-prod,Arn=arn:aws:codepipeline:ap-southeast-1:781462664882:processing-docker-prod,RoleArn=arn:aws:iam::781462664882:role/service-role/events-generic-devops-role`
### CloudTrail put event selectors Docker (s3)
`aws cloudtrail put-event-selectors --cli-input-json file://cloud_trail_code_pipeline_event_selectors.json`