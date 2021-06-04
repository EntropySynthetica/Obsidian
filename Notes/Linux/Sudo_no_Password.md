---
tags: [Linux]
title: Sudo_no_Password
created: '2020-01-30T20:16:15.935Z'
modified: '2020-04-18T01:41:02.926Z'
---

# Sudo no Password
Created Tuesday 06 September 2016

To edit sudo run the following from root or with sudo
`visudo` 


Add user to have sudo access
`myusername    ALL=(ALL:ALL) ALL`

Add user to have sudo with no password ask.
`myusername    ALL=NOPASSWD: ALL`

