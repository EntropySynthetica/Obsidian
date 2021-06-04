---
tags: [Linux]
title: Netstat
created: '2020-01-30T20:16:15.835Z'
modified: '2020-02-02T17:48:11.008Z'
---

# Netstat
Created Wednesday 14 March 2018

## Show listening services

`netstat -tulpn`

## Show active outbound connections

`lsof -i4`

## Show all TCP ports in use:

`netstat -ta`

or

`ss -ta`

or

`lsof -i TCP`

