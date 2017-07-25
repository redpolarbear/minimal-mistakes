---
title: OpenVPN AS: SSL Medium Strength Cipher Suites Supported
date: 2017-07-25
categories: [System Administrator]
tags: [linux, SSL, OpenVPN AS]
---

## Problem
### SSL Medium Strength Cipher Suites Supported
> Here is the list of medium strength SSL ciphers supported by the remote server :
> 
> Medium Strength Ciphers (> 64-bit and < 112-bit key, or 3DES)
> 
> - TLSv1
> > - EDH-RSA-DES-CBC3-SHA Kx=DH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > - ECDHE-RSA-DES-CBC3-SHA Kx=ECDH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > - DES-CBC3-SHA Kx=RSA Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> 
> - TLSv11
> > - EDH-RSA-DES-CBC3-SHA Kx=DH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > - ECDHE-RSA-DES-CBC3-SHA Kx=ECDH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > - DES-CBC3-SHA Kx=RSA Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> 
> - TLSv12
> > - EDH-RSA-DES-CBC3-SHA Kx=DH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > - ECDHE-RSA-DES-CBC3-SHA Kx=ECDH Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
> > - DES-CBC3-SHA Kx=RSA Au=RSA Enc=3DES-CBC(168) Mac=SHA1 
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
- Login to the OpenVPN As server with `root` account.
- Run the script `sacli`.

```js
#cd /usr/local/openvpn_as/scripts
#./sacli -k cs.openssl_ciphersuites -v 'DEFAULT:!EXP:!PSK:!SRP:!LOW:!RC4:!EDH-RSA-DES-CBC3-SHA:!ECDHE-RSA-DES-CBC3-SHA:!DES-CBC3-SHA' ConfigPut
#./sacli start
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
- [OpenVPN Offical Doc - Select Web Server Ciphersuites](https://docs.openvpn.net/docs/access-server/openvpn-access-server-command-line-tools.html#selecting-web-server-ciphersuites)
- [Test bash script](https://superuser.com/questions/109213/how-do-i-list-the-ssl-tls-cipher-suites-a-particular-website-offers)


