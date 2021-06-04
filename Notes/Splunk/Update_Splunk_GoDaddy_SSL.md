---
tags: [Splunk]
title: Update_Splunk_GoDaddy_SSL
created: '2020-01-30T20:16:16.483Z'
modified: '2020-01-30T20:55:18.853Z'
---

# Update Splunk GoDaddy SSL
Created Wednesday 14 February 2018

Splunk uses to files for SSL,

The Private SSL key
[/opt/splunk/etc/auth/arvig/key.key](file:///opt/splunk/etc/auth/cert/key.key)

The CA Cert and Intermedate file
[/opt/splunk/etc/auth/arvig/splunkcert.pem](file:///opt/splunk/etc/auth/cert/splunkcert.pem)

When updating the key file does not need to be changed or replaced.  If the cert if from a new registar the private key is generated locally and used to make
a cert request file *.csr.  

Go Daddy will supply a zip file with to files.  
gd_bundle-g2-g1.crt = The intermediate and root certs
xxxxxxxxxxxx.crt = (X is random letters and numbers) The Certificate file for our cert.  

Splunk expects the cert file and the intermediate cert to be combined into one PEM file.  By default Go Daddy supplies the certs in PEM format, they just need to be combined. 

To do this run the following, 

cat xxxxxxxxxxxxx.crt gd_bundle-g2-g1.crt > splunkcert.pem

Then copy the splunkcert.pem to [/opt/splunk/etc/auth/arvig](file:///opt/splunk/etc/auth/cert) and restart. 

