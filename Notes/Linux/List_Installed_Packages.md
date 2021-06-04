---
tags: [Linux]
title: List_Installed_Packages
created: '2020-01-30T20:16:15.791Z'
modified: '2020-01-30T20:43:52.296Z'
---

# List Installed Packages
Created Tuesday 06 September 2016

#How to list installed packages

dpkg --get-selections | grep -v deinstall > ~/Documents/packages.list

