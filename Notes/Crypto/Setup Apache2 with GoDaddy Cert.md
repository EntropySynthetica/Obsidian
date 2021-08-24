---
tags: [Crypto, Linux]
title: Setup_Apache2_with_GoDaddy_Cert
created: '2020-01-30T20:16:15.911Z'
modified: '2021-01-29T18:01:14.568Z'
---

# Setup Apache2 with GoDaddy Cert

Three files will be needed.  

Private Key, generated on the web server.  

Signed Cert, returned from the CA

CA Intermediate Cert, can be Downloaded from <https://certs.godaddy.com/repository/gd_bundle-g2-g1.crt>

Assuming the following file names,

* Priv Key = `priv.key`
* Cert = `mycert.crt`
* CA = `gd_bundle-g2-g1.crt`

Create a PEM file from these 2,

```bash
cat mycert.crt gd_bundle-g2-g1.crt > mycert.pem
```

In the Conf file for the site make sure to add the following lines, 

```
SSLCertificateFile /etc/apache2/ssl/mycert.pem
SSLCertificateKeyFile /etc/apache2/ssl/priv.key
```

reload Apache with

```bash
sudo service apache2 reload
```

Site can be verified with

```bash
openssl s_client -showcerts -connect www.mysite.com:443
```

```bash
openssl verify -CAfile chain.pem mycert.pem
```


## Verify Cert from CLI
```
openssl x509 -text -noout -in certificate.crt 
```

## Verify Priv key matches cert
For your SSL certificate: 
```bash
openssl x509 –noout –modulus –in <file>.crt | openssl md5
```

For your RSA private key: 
```bash
openssl rsa –noout –modulus –in <file>.key | openssl md5
```

For your CSR: 
```bash
openssl req -noout -modulus -in <file>.csr | openssl md5
```

You just need to replace <file> with your file’s name. If all the three match, the SSL certificate matches the Private Key.
