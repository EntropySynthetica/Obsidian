---
tags: [Linux]
title: Ubuntu_Account_Lockout
created: '2020-01-30T20:16:15.991Z'
modified: '2020-04-16T14:06:47.873Z'
---

# Ubuntu Account Lockout
Created Friday 28 December 2018

Use `/etc/pam.d/common-auth` configuration file to configure login attempts accesses. Open this file and add the following AUTH configuration line to it at beginning of the ‘auth‘ section.

```
auth        required      pam_tally2.so  file=/var/log/tallylog deny=3 even_deny_root unlock_time=1200
```
Next, add the following line to `/etc/pam.d/common-account` section.

```
account     required      pam_tally2.so
```

Now, verify or check the counter that user attempts with the following command.

`pam_tally2 --user=sysadmin`


How to reset or unlock the user account to enable access again.

`pam_tally2 --user=sysadmin --reset`

