---
layout: post
title:  "Add AWS Cloud Build Setup through CLI"
date:   2020-06-22 07:00:00 +0200
categories: devops AWS codebuild codepipeline cloudwatch events cloudtrail
---

## Background
In a project that uses AWS Devops products CodeBuild and CodePipeline to automate continuous delivery, it bugs me every time when I have to setup a pipeline for a new functionality. Funcitionalities here are analytical pipelines, mostly using R code, packaged in docker containers to be run as batch jobs on AWS Batch. Each such functionality is build in two steps, first as package (which is then stored as archieve on S3) and as a docker image on ECR. There exists for each such functionality a develop and production build pipeline. Setting up the devops pipelines for a new funcitonality using the aws console feels awkward and prone to missconfiguration, so I was looking how this could be streamlined using only config files and the awscli. Also, using the console will for each project generate new roles and policies etc. while the needed roles could usually be setup in a reusable way to prevent bloating the account with near identical configuration files.

## Preparation
Preparation is two fold. First a set of configuration objects need to be generated which then live in the AWS account and will be reused:
* generic codebuild policy (use policy statement)
* generic codebuild role (use assume role policy document)
* generic events policy (use policy statement)
* generic events role (use assume role policy document)

### Create CodeBuild Policy
`aws iam create-policy --policy-name codebuild-generic-devops-policy --path /service-role/ --policy-document file://code_build_policy_statement.json`  
The `code_build_policy_statement.json` for generic access needed by code-build:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Resource": [
                "arn:aws:logs:ap-southeast-1:<aws-account-id>:log-group:/aws/codebuild/*",
                "arn:aws:logs:ap-southeast-1:<aws-account-id>:log-group:/aws/codebuild/*:*"
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
                "arn:aws:codecommit:ap-southeast-1:<aws-account-id>:*"
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
                "arn:aws:codebuild:ap-southeast-1:<aws-account-id>:report-group/*"
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

### Create CloudWatch/Events Policy
This policy allows starting codepipelines. It can be used both for the s3 and git triggered events. `aws iam create-policy --policy-name events-generic-devops-role --path /service-role/ --policy-document events_policy_statement.json`  
The `events_policy_statment.json` for generic access needed by code-build:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codepipeline:StartPipelineExecution"
            ],
            "Resource": [
                "arn:aws:codepipeline:ap-southeast-1:<aws-account-id>:*"
            ]
        }
    ]
}
```
### Create CloudWatch/Events Role
The service role assuming aws events identity can be used for both s3 and git triggered events. To create the Role, an `events_assume_role_policy_document.json` is needed:  
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "events.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```
`aws iam create-role --role-name events-generic-devops-role --path /service-role/ --assume-role-policy-document file://events_assume_role_policy_document.json`
### Attach Policy to Role
`aws iam attach-role-policy --role-name events-generic-devops-role --policy-arn arn:aws:iam::781462664882:policy/events-generic-policy-devops`




## Integrate new R Package into cloud build pipeline

### Add CodeBuild Package
Fields to adjust in template: 
* name (packagename-package-prod/dev)
* source/location (codecommit git repo url)
* sourceVersion (refs/heads/master or develop)
* artifacts/path (packagenname-package/)
* artifacts/name (packagename-prod/dev.zip)  
`aws codebuild create-project --cli-input-json file://code_build_project_package_template.json`  


### Add CodeBuild Docker
Fields to adjust in template:
* name (packagename-docker-prod/dev)
* source/secondarySourceVersions/sourceVersion (refs/heads/master or develop)
* environment/environmentVariables/value (IMAGE_REPO_NAME: packagename)
* environment/environmentVariables/value (IMAGE_TAG: latest/develop)  
`aws codebuild create-project --cli-input-json file://code_build_project_docker_template.json`  


### Add CodePipeline Package
Fields to adjust in template:
* pipeline/name (packagename-package-prod/dev)
* pipeline/stages(name:source)/actions/configuration/BranchName (master/develop)
* pipeline/stages(name:source)/actions/configuration/RepositoryName (immunoscape-packagename-package)
* pipeline/stages(name:build)/actions/configuration/ProjectName (packagename-package-prod/dev)
* pipeline/stages(name:store)/actions/configuration/ObjectKey (packagename-package/packagename-prod/dev.zip)  
`aws codepipeline create-pipeline --cli-input-json file://code_pipeline_package_template.json`  


### Add CodePipeline Docker
Fields to adjust in template:
* pipeline/name (packagename-docker-prod/dev)
* pipeline/stages/name:source)/actions/configuration/S3ObjectKey (packagename-package/packagename-prod/dev.zip)
* pipeline/stages(name:build)/actions/configuration/ProjectName (packagename-docker-prod/dev)  
`aws codepipeline create-pipeline --cli-input-json file://code_pipeline_docker_template.json`


### Put Events Rule Package (git)
Fields to adjust in template:
* Name (devops-packagename-prod/dev-git-rule)
* resources (codecommit git repo arn)
* branch/referencename (master/develop)  
`aws events put-rule --cli-input-json file://events_rule_pipeline_git_template.json`  


### Put Events Target Package (git)
Fields to asjust in cli call
* --rule devops-packagename-prod/dev-git-rule
* --targets Id=packagename-package-prod/dev
* --targets Arn=arn_of_pipeline  
`aws events put-targets --rule devops-processing-prod-git-rule --targets Id=processing-package-prod,Arn=arn:aws:codepipeline:ap-southeast-1:781462664882:processing-package-prod,RoleArn=arn:aws:iam::781462664882:role/service-role/events-generic-devops-role`  


### Put Events Rule Docker (S3)
Fields to adjust in template:
* Name (devops-packagename-prod/dev-s3-rule)
* key (packagename-package/packagename-prod/dev.zip)  
`aws events put-rule --cli-input-json file://events_rule_pipeline_s3_template.json`  


### Put Events Target Docker (S3)
Fields to adjust in cli call
* --rule (devops-packagename-prod/dev-s3-rule)
* --targets Id=packagename-docker-prod/dev
* --targets Arn=arn_of_pipeline  
`aws events put-targets --rule devops-processing-prod-s3-rule --targets Id=processing-docker-prod,Arn=arn:aws:codepipeline:ap-southeast-1:781462664882:processing-docker-prod,RoleArn=arn:aws:iam::781462664882:role/service-role/events-generic-devops-role`  


### CloudTrail put event selectors Docker (needed for S3 as trigger)
Fileds to adjust in template:
* Values (add arn for S3 object on which events shall be logged)  
`aws cloudtrail put-event-selectors --cli-input-json file://cloud_trail_code_pipeline_event_selectors.json`  