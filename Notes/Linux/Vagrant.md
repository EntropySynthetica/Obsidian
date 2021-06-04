---
tags: [Linux]
title: Vagrant
created: '2020-01-30T20:16:15.999Z'
modified: '2020-01-30T20:55:37.541Z'
---

# Vagrant
Created Friday 19 January 2018

Boxes are the base image Vagrant uses to create a VM.  They are not modifed by changes to the VM.  

To add a box:
vagrant box add USER/BOX

Search for boxes at
<https://app.vagrantup.com/boxes/search>

To install ubuntu 16.x
vagrant box add ubuntu/xenial64

Next set a folder somewhere on the system.  Put a Vagrant file in that folder.  

