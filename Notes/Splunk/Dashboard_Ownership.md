---
tags: [Splunk]
title: Dashboard_Ownership
created: '2020-01-30T20:16:16.267Z'
modified: '2020-01-30T20:39:00.727Z'
---

# Dashboard Ownership
Created Tuesday 06 September 2016

The permissions and scope of the Splunk objects are defined in the .meta files. Typically there will be an entry in the local.meta file in the /etc/apps/App_Name/metadata folder which will define who is the owner, scope (This app/All Apps) of the object and access permission. A typical entry in local.meta looks like below

 [view/error_lookup]
 access = read : [ * ], write : [ admin ]
 export = system
 owner = admin
When you copied the dashboard from Search app to individual apps, copy the local.meta entries for those dashboards into the corresponding apps as well.

