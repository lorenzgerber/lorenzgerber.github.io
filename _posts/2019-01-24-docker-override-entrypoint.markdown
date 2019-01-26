---
layout: post
title:  "Override the ENTRYPOINT in a Docker container"
date:   2019-01-24 09:34:28 +0800
categories: Docker
---

The ENTRYPOINT of a Dockerfile definition can be overridden:

`docker run -it --entrypoint "/bin/bash" immunoscape-robot`

If further arguments need to be provided to the entrypoint command, they 
should be added at the end:

`docker run --entrypoint "/bin/ls" immunoscape-robot '-alh'`