---
tags: [Linux]
title: SystemD_enable_and_disable_services
created: '2020-01-30T20:16:15.939Z'
modified: '2020-01-30T20:54:11.714Z'
---

# SystemD enable and disable services
Created Wednesday 11 April 2018

List which services are enabled or disabled

sudo systemctl list-unit-files

To enable a service

sudo systemctl enable <serviceName>

To Disable a service

sudo systemctl disable <serviceName>

