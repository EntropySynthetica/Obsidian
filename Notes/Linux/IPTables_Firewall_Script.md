---
tags: [Linux]
title: IPTables_Firewall_Script
created: '2020-01-30T20:16:15.639Z'
modified: '2021-02-04T19:38:01.770Z'
---

# IPTables Firewall Script
Created Tuesday 06 September 2016

```
#!/bin/bash
#
# iptables easy configuration script for Debian
#
#-------------------------
# Flush all current rules from iptables
#
/sbin/iptables -F
#
#-------------------------
# Allow SSH connections on tcp port 22
# This is essential when working on remote servers via SSH to prevent locking yourself out of the system
#
/sbin/iptables -A INPUT -p tcp --dport 22 -j ACCEPT
#
#-------------------------
# Set default policies for INPUT, FORWARD and OUTPUT chains
#
/sbin/iptables -P INPUT DROP
/sbin/iptables -P FORWARD DROP
/sbin/iptables -P OUTPUT ACCEPT
#
#-------------------------
# Set access for localhost
#
/sbin/iptables -A INPUT -i lo -j ACCEPT
#
#------------------------
#iptables -A INPUT -m pkttype --pkt-type multicast -j ACCEPT
#
#-------------------------
# Allow ICMP Ping
#
/sbin/iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
#
#-------------------------
#iptables -A INPUT -s 192.168.21.0/24 -j ACCEPT
#iptables -A INPUT -p udp --dport 20 -j ACCEPT
#iptables -A INPUT -p udp --dport 5060 -j ACCEPT
iptables -A INPUT -p tcp --dport 5555 -j ACCEPT
iptables -A INPUT -p tcp --dport 13666 -j ACCEPT
#
#-------------------------
# Accept packets belonging to established and related connections
#
/sbin/iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
#
#-------------------------
# Save settings if we are in CentOS
#
iptables-save > /root/firewall.rules
#
#-------------------------
# List rules
#
/sbin/iptables -L -v
```




## Notes

Unblock an Interface
`iptables -A INPUT -i eth0 -j ACCEPT`

Accept packets from trusted IP addresses
`iptables -A INPUT -s 192.168.0.4 -j ACCEPT` # change the IP address as appropriate

Accept packets from trusted IP addresses
`iptables -A INPUT -s 192.168.0.0/24 -j ACCEPT`  # using standard slash notation
`iptables -A INPUT -s 192.168.0.0/255.255.255.0 -j ACCEPT` # using a subnet mask

Accept tcp packets on destination port 6881 (bittorrent)
`iptables -A INPUT -p tcp --dport 6881 -j ACCEPT`
Note: In order to use matches such as destination or source ports (--dport or --sport), you must first specify the protocol 
(tcp, udp, icmp, all). 

Accept tcp packets on destination port 6881 (bittorrent)
`iptables -A INPUT -p tcp --dport 6881 -j ACCEPT`

Accept tcp packets on destination ports 6881-6890
`iptables -A INPUT -p tcp --dport 6881:6890 -j ACCEPT`

Accept tcp packets on destination port 22 (SSH) from private LAN
`iptables -A INPUT -p tcp -s 192.168.0.0/24 --dport 22 -j ACCEPT`

Show hit for rules with auto refresh
`watch --interval 0 'iptables -nvL | grep -v "0     0"'`

Show hit for rule with auto refresh and highlight any changes since the last refresh
`watch -d -n 2 iptables -nvL`

Block the port 902 and we hide this port from nmap.
`iptables -A INPUT -i eth0 -p tcp --dport 902 -j REJECT --reject-with icmp-port-unreachable`

Add a comment to a rule:
`iptables ... -m comment --comment "This rule is here for this reason"`

## Iptables Persistant in Debian
install the following
`apt-get install iptables-persistent`

To Save
`sudo dpkg-reconfigure iptables-persistent`

or on Ubuntu 20 
`sudo iptables-save > /etc/iptables/rules.v4`





