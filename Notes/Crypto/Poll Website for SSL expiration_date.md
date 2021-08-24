---
tags: [Crypto, Linux]
title: Poll_Website_for_SSL_expiration_date
created: '2020-01-30T20:16:15.859Z'
modified: '2020-02-12T21:29:41.744Z'
---

# Poll Website for SSL expiration date
Created Friday 04 May 2018

```bash
openssl s_client -connect site.foo.bar:443 < /dev/null 2>/dev/null | openssl x509 -noout -enddate
```

Example:

```bash
openssl s_client -connect www.google.com:443 < /dev/null 2>/dev/null | openssl x509 -noout -enddate
notAfter=Apr 22 22:01:37 2020 GMT
```

