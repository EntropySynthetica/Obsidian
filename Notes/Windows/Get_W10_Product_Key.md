---
tags: [Windows]
title: Get_W10_Product_Key
created: '2020-01-30T20:16:16.539Z'
modified: '2020-02-02T17:56:48.416Z'
---

# Get W10 Product Key
Created Friday 15 June 2018

Via Powershell,

`(Get-WmiObject -query ‘select * from SoftwareLicensingService’).OA3xOriginalProductKey`

