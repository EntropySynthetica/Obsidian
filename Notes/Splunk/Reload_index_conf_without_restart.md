---
tags: [Splunk]
title: Reload_index_conf_without_restart
created: '2020-01-30T20:16:16.315Z'
modified: '2020-01-30T20:50:30.667Z'
---

# Reload index conf without restart
Created Friday 22 September 2017

$SPLUNK_HOME/bin/splunk reload index

- reloads index configuration, making immediatley effective all "add/edit/enable/disable index" commands since last reload or splunk restart

Why?

- Adding a new app
- Changing a frozen time period
- New location for data

