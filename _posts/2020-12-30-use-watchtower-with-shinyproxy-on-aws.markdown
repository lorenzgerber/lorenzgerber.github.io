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
- build container
- create Docker Volume for helper
- run docker container to place ECR credential helper in Docker Volume
2. Create Json configuration file for Docker
3. Create docker-compose file

Code snippets for all steps are avaialable in the wathtower doc section about private registries[private-registries].

[priavate-registries]: https://containrrr.dev/watchtower/private-registries/
[watchtower-github]: https://containrrr.dev/watchtower/ 




