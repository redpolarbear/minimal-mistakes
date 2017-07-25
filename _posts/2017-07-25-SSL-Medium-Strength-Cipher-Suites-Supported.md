---
title: SSL Medium Strength Cipher Suites Supported
date: 2017-07-25
categories: [System Administrator]
tags: [linux, SSL]
---

## Problem
### SSL Medium Strength Cipher Suites Supported
> Here is the list of medium strength SSL ciphers supported by the remote server :
> 
> Medium Strength Ciphers (> 64-bit and < 112-bit key, or 3DES)
> 
> - SSLv3
> > * EDH-RSA-DES-CBC3-SHA Kx=DH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > * DES-CBC3-SHA Kx=RSA Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> 
> - TLSv1
> > * EDH-RSA-DES-CBC3-SHA Kx=DH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > * DES-CBC3-SHA Kx=RSA Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> 
> The fields above are :
> 
> {OpenSSL ciphername}
> Kx={key exchange}
> Au={authentication}
> Enc={symmetric encryption method}
> Mac={message authentication code}
> {export flag}

## Solution
- Edit the configuration file - `/etc/httpd/conf.d/ssl.conf`.
- Find `#   SSL Cipher Suite:`
- Append the cipher we'd like to disabled by adding `!` in front.
```
SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HIGH:!MEDIUM:!LOW:!DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA
```

## Verfication
1. Use the [ssllabs.com](https://www.ssllabs.com/ssltest/index.html) to test.
2. Use bash script to test as below

```bash
#!/usr/bin/env bash

# OpenSSL requires the port number.
SERVER=<server hostname or ip address>:443
DELAY=1
ciphers=$(openssl ciphers 'ALL:eNULL' | sed -e 's/:/ /g')

echo Obtaining cipher list from $(openssl version).

for cipher in ${ciphers[@]}
do
echo -n Testing $cipher...
result=$(echo -n | openssl s_client -cipher "$cipher" -connect $SERVER 2>&1)
if [[ "$result" =~ ":error:" ]] ; then
  error=$(echo -n $result | cut -d':' -f6)
  echo NO \($error\)
else
  if [[ "$result" =~ "Cipher is ${cipher}" || "$result" =~ "Cipher    :" ]] ; then
    echo YES
  else
    echo UNKNOWN RESPONSE
    echo $result
  fi
fi
sleep $DELAY
done
```

Refer to: 
- [http://httpd.apache.org/docs/2.2/mod/mod_ssl.html#sslciphersuite](http://httpd.apache.org/docs/2.2/mod/mod_ssl.html#sslciphersuite)
- [https://serverfault.com/questions/123917/how-do-i-disable-medium-and-weak-low-strength-ciphers-in-apache-mod-ssl](https://serverfault.com/questions/123917/how-do-i-disable-medium-and-weak-low-strength-ciphers-in-apache-mod-ssl) 
- [https://www.centos.org/forums/viewtopic.php?t=30918](https://www.centos.org/forums/viewtopic.php?t=30918)
- [Test bash script](https://superuser.com/questions/109213/how-do-i-list-the-ssl-tls-cipher-suites-a-particular-website-offers)


