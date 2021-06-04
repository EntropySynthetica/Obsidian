---
tags: [Splunk]
title: Event_Parsing_Efficency
created: '2020-01-30T20:16:16.271Z'
modified: '2020-01-30T20:40:39.226Z'
---

# Event Parsing Efficency
Created Thursday 21 September 2017

*Line breaking

-Avoid using Line Merging such as
SHOULD_LINEMERGE = true
BREAK_ONLY_BEFORE_DATE
BREAK_ONLY_BEFORE
MUST_BREAK_AFTER
MUST_NOT_BREAK_AFTER
etc

- LINE_BREAKER is much more efficent

props.conf

[my_soucetype]
SHOULD_LINEMERGE = false
LINE_BREAKER= regex_statement


* Uses regex to determine when the raw text should be broken into individual events


