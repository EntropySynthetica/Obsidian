---
tags: [Linux]
title: Current_Kerberos_Password
created: '2020-01-30T20:16:15.463Z'
modified: '2020-01-30T20:38:54.751Z'
---

# Current Kerberos Password
Created Tuesday 06 September 2016

in your /etc/pam.d/common-password , change the minimum_uid in your first line to something bigger than 1000, example:

password        [success=3 default=ignore]      pam_krb5.so minimum_uid=10000

