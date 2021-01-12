---
layout: post
title:  "use watchtower with shinyproxy on aws"
date:   2020-12-30 09:40:00 +0200
categories: docker aws
---

## Background
I am currently running a dockerized shinyproxy setup on aws with a couple of 
shiny apps served to in-house users. This is part of a larger setup with other analytical pipelines
running as batch jobs on aws. The batch pipelines are docker containers themself and are versioned with
docker tags for reproducibility, quick update and rollbacks in case of hiccups. This however does not
work in a similar way for the shinyproxy server. Hence I setup a watchtower daemon that checks regularly 
whether a new image of the container in question is available at the repository and if so, pulls and restarts
it.

## Some more details
[Watchtower][watchtower-github] is described on the github page as 'A process for automating Docker container base image updates'. Generally, watchtower will check and update all running or exited containers, not plain images. Watchtower comes as
docker image itself and works without much config when using public repositories. However, for using AWS, some
more configuration and setup is needed. This blogpost with sum up what's needed for that. 

## Howto
1. Generate Volume that contains credential helper
- Create Dockerfile to build ECR credential helper  

    ```
    FROM golang:latest

    ENV CGO_ENABLED 0
    ENV REPO github.com/awslabs/amazon-ecr-credential-helper/ecr-login/cli/docker-credential-ecr-login

    RUN go get -u $REPO

    RUN rm /go/bin/docker-credential-ecr-login

    RUN go build \
    -o /go/bin/docker-credential-ecr-login \
    /go/src/$REPO

    WORKDIR /go/bin/
    ```

    - build container  
    `docker build -t aws-ecr-dock-cred-helper .`
    - create Docker Volume for helper  
    `docker volume create helper`
    - run docker container to place ECR credential helper in Docker Volume  
    `docker run -d --rm --name aws-cred-helper --volume helper:/go/bin aws-ecr-dock-cred-helper`

2. Create Json configuration file for Docker (as `~/.docker/config.json`)

    ```
    {
        "credsStore" : "ecr-login",
        "HttpHeaders" : {
        "User-Agent" : "Docker-Client/19.03.1 (XXXXXX)"
        },
        "auths" : {
        "<AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com" : {}
        },
        "credHelpers": {
        "<AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com" : "ecr-login"
        }
    }
    ```

3. Create docker-compose file  

    ```
    version: "3.4"
    services:
    # Check for new images and restart things if a new image exists
    # for any of our containers.
    watchtower:
        image: containrrr/watchtower:latest
        volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - /home/ubuntu/.docker/config.json:/config.json
        - helper:/go/bin
        environment:
        - HOME=/
        - PATH=$PATH:/go/bin
        - AWS_REGION=ap-southeast-1
        - AWS_ACCESS_KEY_ID=key_id
        - AWS_SECRET_ACCESS_KEY=secret_access_key
        command: --interval 30 --include-stopped
    volumes:
    helper: 
        external: true
    ```

4. Start service  
`docker-compose up -d`


Code snippets are taken from the watchtower doc section about private registries[private-registries].

[priavate-registries]: https://containrrr.dev/watchtower/private-registries/
[watchtower-github]: https://containrrr.dev/watchtower/ 




