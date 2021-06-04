---
tags: [Splunk]
title: Refresh_Manual_Dashboard_Changes
created: '2020-01-30T20:16:16.311Z'
modified: '2020-01-30T20:50:19.699Z'
---

# Refresh Manual Dashboard Changes
Created Wednesday 12 July 2017

If direct edits are made to Splunks XML files a refresh is required to show the updates.  

Adding [/debug/refresh](file:///debug/refresh) to your splunk server URL will bring up the refresh screen. 

Assuming the URL of our Splunk server is <https://test.splunk.com> you would go to <https://test.splunk.com/debug/refresh>

