---
title: SSH vulnerabilities: HMAC algorithms and CBC ciphers
date: 2017-07-24
categories: [System Administrator]
tags: [linux, ssh]
---

## Problem
### SSH Weak Algorithms Supported
> The following weak server-to-client encryption algorithms are supported : 
>
> * arcfour
> * arcfour128
> * arcfour256
> 
> The following weak client-to-server encryption algorithms are supported : 
> 
> * arcfour
> * arcfour128
> * arcfour256

## Solution
Change the configuration in the `/etc/ssh/sshd_config` on CentOS.

```bash
# default is aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,
# aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,
# aes256-cbc,arcfour
# you can removed the cbc ciphers by adding the line

Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,arcfour

# default is hmac-md5,hmac-sha1,hmac-ripemd160,hmac-sha1-96,hmac-md5-96
# you can remove the hmac-md5 MACs with

MACs hmac-sha1,hmac-ripemd160
```

Once it's done and restart the `sshd` service.

```js
#service sshd restart
```

Also we could test for the issue

```
#ssh -vv -oCiphers=aes128-cbc,3des-cbc,blowfish-cbc <server>
#ssh -vv -oMACs=hmac-md5 <server>
```

We should get the following errors:

> no matching cipher found: client aes128-cbc,3des-cbc,blowfish-cbc server aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,arcfour
