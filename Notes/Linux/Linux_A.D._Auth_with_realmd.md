---
tags: [Linux]
title: Linux_A.D._Auth_with_realmd
created: '2020-01-30T20:16:15.751Z'
modified: '2020-01-30T20:43:30.791Z'
---

# Linux A.D. Auth with realmd
Created Wednesday 31 January 2018

Sourced from, <https://www.reddit.com/r/linuxadmin/comments/4z8y3i/cross_distro_centos7_ubuntu_1604_sssdrealmd/>
Another source is at, <http://blog.admindiary.com/integrate-ubuntu-active-directory-using-kerberos-realmd-sssd/>

Install prerequisites

This varies based on the underlying distro.

RHEL / CentOS

yum install adcli krb5-workstation oddjob oddjob-mkhomedir realmd samba-common samba-common-tools samba-libs sssd sssd-tools

Debian / Ubuntu

apt-get install adcli krb5-user realmd samba-common-bin samba-libs sssd sssd-tools libnss-sss libpam-sss ntp packagekit

Discover realm (Active Directory Domain)

We just need to ensure that your host can see the Domain.. If not, there are other issues at play. In this example, I will use EXAMPLE.COM.

realm discover EXAMPLE.COM

You should see output like this:

[root@hostname ~]# realm discover -v EXAMPLE.COM

* Resolving: _ldap._tcp.example.com
* Performing LDAP DSE lookup on: XXX.XXX.XXX.XXX
* Performing LDAP DSE lookup on: XXX.XXX.XXX.XXX
* Successfully discovered: EXAMPLE.COM

EXAMPLE.COM
type: kerberos
realm-name: EXAMPLE.COM
domain-name: EXAMPLE.COM
configured: no
server-software: active-directory
client-software: sssd
required-package: oddjob
required-package: oddjob-mkhomedir
required-package: sssd
required-package: adcli
required-package: samba-common
example.com
type: kerberos
realm-name: EXAMPLE.COM
domain-name: example.com


Edit /etc/sssd/sssd.conf, creating the file if it does not already exist.

Ensure it contains the following:

[nss]
filter_groups = root
filter_users = root
reconnection_retries = 3

[pam]
reconnection_retries = 3

[service]
automatic-install = no
Obtain Kerberos ticket

To properly join the realm, you will need a valid Kerberos ticket for a Domain user with permission to join a system to the Domain. In this example, I will use EXAMPLE\harambe. Type the following and provide the account password when prompted:

kinit [harambe@EXAMPLE.COM](mailto:harambe@EXAMPLE.COM)

Verify that the Kerberos ticket was obtained successfully by executing the following command:

klist

Join the realm

Execute the following command, remembering to substitute HOSTNAME for the actual hostname (not FQDN) of the system and harambe for the actual user for which you have a Kerberos ticket:

realm --verbose join EXAMPLE.COM --[user-principal=HOSTNAME/harambe@EXAMPLE.COM](mailto:user-principal=HOSTNAME/harambe@EXAMPLE.COM) --unattended

If the realm join was successful, you will notice that /etc/sssd/sssd.conf was populated with a bunch of new things. Edit this file and find the following line:

use_fully_qualified_names = True
Comment it out using the # character at the start of the line, so it looks like this:

# use_fully_qualified_names = True
This will ensure that you won't have to type something like [harambe@HOSTNAME.COM](mailto:harambe@HOSTNAME.COM) every time you attempt to log in, and instead you can just type harambe.

Also, the default home directory format, /[home/username@EXAMPLE.COM](mailto:home/username@EXAMPLE.COM), is kind of weird. Personally, I like to change it to /home/EXAMPLE.COM/username. To do this, edit /etc/sssd/sssd.conf again and find the following line:

fallback_homedir = /home/%u@%d
Replace it with the following:

fallback_homedir = /home/%d/%u
Login permissions

Before we grant access to anybody, deny all users by default. If this isn't done, the Domain Users group has access by default (just like in Windows).

Execute the following command:

realm deny --all

Now grant access to authorized users. This can be done by username or by group name.

User:

realm permit -R EXAMPLE.COM harambe

Group:

realm permit -R EXAMPLE.COM -g linuxadmins

To view all users and groups that currently have access to the host, execute the following command:

realm list

You should see output similar to the following.

[root@hostname ~]$ realm list
EXAMPLE.COM
type: kerberos
realm-name: EXAMPLE.COM
domain-name: example.com
configured: kerberos-member
server-software: active-directory
client-software: sssd
required-package: oddjob
required-package: oddjob-mkhomedir
required-package: sssd
required-package: adcli
required-package: samba-common-tools
login-formats: %U
login-policy: allow-permitted-logins
permitted-logins: harambe
permitted-groups: linuxadmins
Automatic home directory creation

To enable the automatic creation of home directories for domain users, modify the following files:

RHEL / CentOS

/etc/pam.d/login
/etc/pam.d/sshd
Debian / Ubuntu

/etc/pam.d/common­session
Add the following to the bottom of the relevant file(s):

session     required     pam_mkhomedir.so skel=/etc/skel/ umask=0022
Granting sudo privileges

To grant a user access to run commands via sudo , edit the /etc/group file and add the user to either the wheel (RHEL/CentOS) group or sudo (Debian/Ubuntu) group.

Add the following to the /etc/sudoers file to allow all members of the linuxadmins Domain group to execute commands via sudo:

%linuxadmins    ALL=(ALL)        ALL
Conclusion

That's it! You should now be able to log in to the system using your Domain credentials, both locally and via SSH. Your home directory will be created at /home/EXAMPLE.COM/username by default.

