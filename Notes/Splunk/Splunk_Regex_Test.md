---
tags: [Splunk]
title: Splunk_Regex_Test
created: '2020-01-30T20:16:16.475Z'
modified: '2020-01-30T20:53:42.073Z'
---

# Splunk Regex Test
Created Friday 22 September 2017

$SPLUNK_HOME/bin/splunk cmd pcregextest

– Useful	for	tes<ng	regular	expressions	for	extrac<ons	

splunk cmd pcregextest mregex="ip:src_ ip:dst_" ip="(?<ip>\d+dotnum{3})" dotnum="\.\d+" test_str="1.1.1.1 2.2.2.2"

Original Pattern: 'ip:src_ ip:dst_'
Expanded Pattern: '(?<src_ip>\d+(?:\.\d+){3}) (?<dst_ip>\d+(?:\.\d+){3})'
Regex compiled successfully. Capture group count = 2. Named capturing groups = 2.
SUCCESS - match against: '1.1.1.1 2.2.2.2'

#### Capturing group data #####
Group | Name | Value

*****

  1 | 	src_ip 	| 1.1.1.1
 2 | 	dst_ip 	| 2.2.2.2 

