---
tags: [Linux]
title: Set SNMPV3 on Ubuntu 16.04
created: '2020-05-14T15:34:58.244Z'
modified: '2020-05-14T15:41:05.446Z'
---

# Set SNMPV3 on Ubuntu 16.04

Move /etc/snmp/snmpd.conf to /etc/snmp/snmpd.conf.bak

Make a new file named /etc/snmp/snmpd.conf and put the following in it.



```
createUser orion SHA <auth_key> AES <encryp_key>

dontLogTCPWrappersConnects yes

rouser <snmp_username> priv 1.3.6.1.2.1

syslocation "Put Site Here"
syscontact "System Administrator (sysadmin@site.com)"
```

Next edit /etc/default/snmpd

Comment out the 11th line of the file that looks like this

`SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -g snmp -I -smux,mteTrigger,mteTriggerConf -p /run/snmpd.pid'`

Add a new line that looks like this

`SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid -c /etc/snmp/snmpd.conf'`

restart snmp with `sudo service snmpd restart`

check it started ok with `sudo service snmpd status`

Do a test from another machine with, replace x.x.x.x with the IP of the machine you are testing to. 

`snmpget -u <snmp_username> -l authPriv -a SHA -x AES -A <auth_key> -X <encryp_key> x.x.x.x 1.3.6.1.2.1.1.1.0`

