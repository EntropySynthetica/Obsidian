---
tags: [Splunk]
title: Output_CSV
created: '2020-01-30T20:16:16.303Z'
modified: '2020-01-30T20:46:52.445Z'
---

# Output CSV
Created Tuesday 06 September 2016

The basic command to output files is the |outputcsv <filename> in your search. The output of this command goes to the $SPLUNK_HOME/var/run/splunk directory.


So a very basic example would be along the lines of:

index=os |outputcsv text.csv


So you would see $SPLUNK_HOME/var/run/splunk/test.csv as the end result.

