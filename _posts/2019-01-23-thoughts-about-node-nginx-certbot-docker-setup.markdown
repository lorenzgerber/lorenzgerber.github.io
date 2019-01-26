---
layout: post
title:  "Thoughts on Node.js, Nginx, Certbot and Docker setup"
date:   2019-01-23 09:34:28 +0800
categories: node nginx certbot docker
---

How to setup a scalable Node.js backend? The service should use SSL and be deployable in the cloud using docker. Initially I tried to setup a Node.js app in docker using certbot with *Let's encrypt* certificates and Nginx as reverse proxy following a [Digital Ocean tutorial][digital-ocean-tutorial]. However, there are quite a number of manual steps, and it's not yet clear to me how the whole setup could be adapted for scalable depolyments.

Reading more about Nginx/Docker setups, I found out about  [docker-gen][docker-gen]
which seems to cover, among others specifically the case of configuring a reverse proxy on the fly from container meta data. This seems to discuss an issue that I haven't even thought about: When node container come up or go down, the nginx configuration needs to change. This seems however to be a problem when everything is running in docker container. And [docker-gen][docker-gen] is apparently the solution to this.

However, I'm still wondering if there is a way to first obtain the *let's encrypt* certificiate using certbot and then configure a docker container with is ready to run SSL connections after coming up.

Another issue that I don't understand yet is whether/which part of the setup becomes redundant when using AWS CloudFront. Update: Apparently, using AWS Cloudfront and AWS Certificate Manager (ACM), the SSL part can be off loaded to AWS. Public certificates from Amazon Certificate Authority (Amazon CA) are free of charge. Both email or DNS challenge for verification of the certificates are supported.

[digital-ocean-tutorial]: https://www.digitalocean.com/community/tutorials/how-to-secure-a-containerized-node-js-application-with-nginx-let-s-encrypt-and-docker-compose

[docker-gen]: https://github.com/jwilder/docker-gen