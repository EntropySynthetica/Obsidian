---
tags: [Splunk]
title: Restore_Frozen_Data
created: '2020-01-30T20:16:16.327Z'
modified: '2020-01-30T20:50:36.860Z'
---

# Restore Frozen Data
Created Tuesday 06 September 2016

Section 1: Restore “frozen” data:

A pre-requisite for this procedure to work is that the thawedPath variable must be set to a valid mass-storage area. For the upcoming example this has been set to:
thawedPath = $SPLUNK_DB/access/thaweddb


The basic steps for restoring frozen data:
1) Restore the selected frozen bucket to the thawedPath directory:
cp –r <frozen data bucket directory> <thawedPath>

2) Rebuild the data
/opt/splunk/bin splunk rebuild <frozen bucket directory> <[index name (optional)]>

3) Restart splunk

4) Verify data is present


Example:

The frozen data can be stored in a separate area and for the example for this exercise this has been set to:
~/frozen/access/frozendb/

For the purposes of this SOP, the current date is 25-July 2016 and data is “frozen”/archived after 7 days time.  In the data archive the user is to restore data from 11 July 2016 at 13:45 hours. It is noted that there is a frozen data bucket in the archives that contains this data marked by epoch time.


