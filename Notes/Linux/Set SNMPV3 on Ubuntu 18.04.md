---
tags: [Linux]
title: Set SNMPV3 on Ubuntu 18.04
created: '2020-04-17T19:54:39.584Z'
modified: '2020-04-17T19:59:04.501Z'
---

# Set SNMPV3 on Ubuntu 18.04

Install the SNMPD service
`sudo apt install snmp snmpd libsnmp-dev`

Stop SNMP
`sudo service snmpd stop`

Edit `/etc/snmp/snmpd.conf` to look like the following

```
group   notConfigGroup v1           notConfigUser
group   notConfigGroup v2c           notConfigUser

view    systemview    included   .1.3.6.1.2.1.1
view    systemview    included   .1.3.6.1.2.1.25.1.1


access  notConfigGroup ""      any       noauth    exact  systemview none none

dontLogTCPWrappersConnects yes

syslocation "My Location"
syscontact "System Administrator (sysadmin@site.com)"

rouser <myusername>
```

Set your password
```
sudo net-snmp-config --create-snmpv3-user -ro -A <auth key> -X <encryption key> -a SHA -x AES <myusername>
```

Test from another system
```
snmpget -u <myusername> -l authPriv -a SHA -x AES -A <auth key> -X <encryption key> <my host> 1.3.6.1.2.1.1.1.0
```
