---
tags: [Linux]
title: Install_from_Backports
created: '2020-01-30T20:16:15.607Z'
modified: '2020-01-30T20:42:32.847Z'
---

# Install from Backports
Created Wednesday 14 September 2016

Add this to [/etc/apt/sources.list](file:///etc/apt/sources.list)

deb <http://ftp.debian.org/debian> jessie-backports main

sudo apt-get -t jessie-backports install "package"

