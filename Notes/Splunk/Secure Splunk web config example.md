---
tags: [Splunk]
title: Secure Splunk web config example
created: '2020-02-12T21:47:22.206Z'
modified: '2020-02-12T21:49:29.693Z'
---

# Secure Splunk web config example

The following config ensures Splunks SSL settings will pass SSLChecker with an A. 

```
[settings]
enableSplunkWebSSL = 1
httpport = 443
privKeyPath = etc/auth/mycert/key.key
caCertPath = etc/auth/mycert/splunkcert.pem
sslVersions = tls1.2
cipherSuite = ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256
supportSSLV3Only = False
ecdhCurveName = prime256v1,secp384r1,secp521r1
tools.sessions.timeout = 1440
```
