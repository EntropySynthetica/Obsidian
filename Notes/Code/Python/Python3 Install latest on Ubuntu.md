---
tags: [Code]
title: Python3 Install latest on Ubuntu
created: '2020-07-28T13:40:32.443Z'
modified: '2020-07-28T13:42:41.699Z'
---

# Python3 Install latest on Ubuntu

Install Pre-reqs
`sudo apt install software-properties-common`

Add the Python PPA
`sudo add-apt-repository ppa:deadsnakes/ppa`

Update Apt Cache
`sudo apt update`

Install Python 3.8
`sudo apt install python3.8 python3.8-venv`

Verify the installed version
`python3.8 --version`
