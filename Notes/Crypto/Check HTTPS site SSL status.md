---
tags: [Crypto, Linux]
title: Check HTTPS site SSL status
created: '2020-02-12T21:16:02.119Z'
modified: '2020-02-12T21:27:24.378Z'
---

# Check HTTPS site SSL status

## Checking Enabled SSL Ciphers
nmap can poll a site and return what SSL ciphers are enabled.  

`nmap --script ssl-enum-ciphers -p 443 foo.bar.com`

Example:

```
Starting Nmap 7.70 ( https://nmap.org ) at 2020-02-11 10:39 CST
Nmap scan report for foo.bar.com (1.2.3.4)
Host is up (0.034s latency).
rDNS record for 1.2.3.4: 1.2.3.4.bar.com

PORT    STATE SERVICE
443/tcp open  https
| ssl-enum-ciphers: 
|   TLSv1.2: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (secp256r1) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (secp256r1) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 2048) - A
|     compressors: 
|       NULL
|     cipher preference: client
|_  least strength: A
```

Example result of a site that returns A grade security on ssllabs checker

```
Starting Nmap 7.70 ( https://nmap.org ) at 2020-02-11 10:40 CST
Nmap scan report for foo.site.com (1.2.3.4)
Host is up (0.033s latency).

PORT    STATE SERVICE
443/tcp open  https
| ssl-enum-ciphers: 
|   TLSv1.2: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|     compressors: 
|       NULL
|     cipher preference: server
|_  least strength: A

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```

## SSL Site Experation date. 

The following script can check for the SSL expiration date of a site.

https://github.com/skx/sysadmin-util/blob/master/ssl-expiry-date

Example Usage:

```
./ssl-expiry-date www.google.com
www.google.com
    Expires: Apr 14 08:16:35 2020 GMT
    Days: 61
```
