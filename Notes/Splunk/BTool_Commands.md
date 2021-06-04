---
tags: [Splunk]
title: BTool_Commands
created: '2020-01-30T20:16:16.231Z'
modified: '2020-01-30T20:36:38.971Z'
---

# BTool Commands
Created Tuesday 06 September 2016

BTOOL commands

For EVENTGEN for checkpoint
grep "Creating timer object for sample" eventgen.log /opt/splunk/var/log/splunk/eventgen.log | grep -i checkpoint



/opt/splunk/bin/splunk btool app list install --debug

/opt/splunk/bin/splunk btool eventgen list --debug | grep main

/opt/splunk/bin/splunk btool eventgen list --debug | grep Splunk_TA_nix | grep index

/opt/splunk/bin/splunk btool eventgen list --debug | grep nix | grep "\[" | grep -v token

splunk btool props list volume-iops --debug | grep -v system/default


splunk cmd btool indexes list --debug| grep onboarding 

splunk cmd btool props list --debug|grep iis_onboarding 

splunk btool server list clustering --debug| grep mode

splunk cmd btool tags list --debug|grep auth

splunk cmd btool eventtypes list --debug

splunk cmd btool lookups list --debug|grep csv

splunk cmd btool indexes list —-debug| grep -v system/

splunk cmd btool outputs list --debug| grep server

This command will tell you which app the indexes.conf _blocksignature stanza resides in:
splunk cmd btool --debug-print=app indexes list _blocksignature | head -1 | awk '{print $1}'


To list all of the indexes 
 ./splunk cmd btool indexes list 

To see what you are sending to the indexer FROM the Forwarder 
splunk cmd btool inputs list --debug 

splunk btool server list clustering --debug 




