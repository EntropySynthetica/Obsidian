---
tags: [Docker]
title: Dockerhub
created: '2020-07-15T19:36:34.165Z'
modified: '2020-07-15T19:57:38.324Z'
---

# Dockerhub

## Create a Token and login 

To use the access token from your Docker CLI client:
1. Run `docker login --username <username>`
2. At the password prompt, enter the personal access token.

To build your image from a Dockerfile
`docker build -t <username>/<reponame>:<tag> .`

To push your image to Dockerhub
`docker push <username>/<reponame>:<tag>`


