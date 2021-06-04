---
tags: [Splunk]
title: One_Shot
created: '2020-01-30T20:16:16.299Z'
modified: '2020-01-30T20:46:47.397Z'
---

# One Shot
Created Tuesday 06 September 2016

One Shot Command
splunk add oneshot -source edifecs-sample.log -sourcetype edifecs -index onboarding

To clean data after the One-shot command
stop splunk
/opt/splunk/bin/splunk clean eventdata -index <index name>

props.conf basic
[name]
TIME_PREFIX
TIME_FORMAT
MAX_TIMESTAMP_LOOKAHEAD
SHOULD_LINEMERGE
LINE_BREAKER
TRUNCATE

DATETIME_CONFIG - use for datetime.xml  and if you have no timestamp in the data DATETIME_CONFIG = CURRENT

To add a user named joshua to your Splunk Instance
splunk add user joshua -role admin -password chess



