---
tags: [Linux]
title: NTP Config
created: '2020-03-09T20:28:40.525Z'
modified: '2020-03-09T20:34:31.628Z'
---

# NTP Config

## Secure NTP Config file

```
# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntp/ntp.drift

# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

# Specify one or more NTP servers.

# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
pool 0.ubuntu.pool.ntp.org iburst
pool 1.ubuntu.pool.ntp.org iburst
pool 2.ubuntu.pool.ntp.org iburst
pool 3.ubuntu.pool.ntp.org iburst

# Use Ubuntu's ntp server as a fallback.
pool ntp.ubuntu.com

# Access control configuration; see /usr/share/doc/ntp-doc/html/accopt.html for
# details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# might also be helpful.
#
# Note that "restrict" applies to both servers and clients, so a configuration
# that might be intended to block requests from certain clients could also end
# up blocking replies from your own upstream servers.

# By default, exchange time with everybody, but don't allow configuration.
#http://support.ntp.org/bin/view/Support/AccessRestrictions
#6.5.1.1.3 Allow Queries
restrict -4 default kod notrap nomodify nopeer noquery
restrict -6 default kod notrap nomodify nopeer noquery

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# Needed for adding pool entries
restrict source notrap nomodify noquery

# Clients from this (example!) subnet have unlimited access, but only if
# cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust


# If you want to provide time to your local subnet, change the next line.
# (Again, the address is an example only.)
#broadcast 192.168.123.255

# If you want to listen to time broadcasts on your local subnet, de-comment the
# next lines.  Please do this only if you trust everybody on the network!
#disable auth
#broadcastclient

#Changes recquired to use pps synchonisation as explained in documentation:
#http://www.ntp.org/ntpfaq/NTP-s-config-adv.htm#AEN3918

#server 127.127.8.1 mode 135 prefer    # Meinberg GPS167 with PPS
#fudge 127.127.8.1 time1 0.0042        # relative to PPS for my hardware

#server 127.127.22.1                   # ATOM(PPS)
#fudge 127.127.22.1 flag3 1            # enable PPS API

```

## Check Status of NTP Server

Command to check NTP Status

`sudo nmap -sU -p 123 --script ntp-info <server ip>`

Reply from non-secure server

```
Starting Nmap 7.70 ( https://nmap.org ) at 2020-03-09 14:49 CDT
Nmap scan report for 172.19.16.51
Host is up (0.0083s latency).

PORT    STATE SERVICE
123/udp open  ntp
| ntp-info: 
|   receive time stamp: 2020-03-09T19:49:49
|   version: ntpd 4.2.8p4@1.3265-o Tue Jan  7 15:08:23 UTC 2020 (1)
|   processor: x86_64
|   system: Linux/4.4.0-174-generic
|   leap: 3
|   stratum: 16
|   precision: -24
|   rootdelay: 0.000
|   rootdisp: 0.060
|   refid: INIT
|   reftime: 0x00000000.00000000
|   clock: 0xe2111c53.b6a7db6e
|   peer: 0
|   tc: 3
|   mintc: 3
|   offset: 0.000000
|   frequency: 81.012
|   sys_jitter: 0.000000
|   clk_jitter: 0.000
|_  clk_wander: 0.000\x0D
Service Info: OS: Linux/4.4.0-174-generic

Nmap done: 1 IP address (1 host up) scanned in 0.84 seconds

```

Reply for Secure Server

```
Starting Nmap 7.70 ( https://nmap.org ) at 2020-03-09 14:55 CDT
Nmap scan report for 172.19.16.50
Host is up (0.0080s latency).

PORT    STATE SERVICE
123/udp open  ntp
| ntp-info: 
|_  receive time stamp: 2020-03-09T19:55:46

Nmap done: 1 IP address (1 host up) scanned in 10.75 seconds

```
