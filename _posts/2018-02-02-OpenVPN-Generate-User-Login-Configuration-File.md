---
title: OpenVPN - Generate User Login Configuration File (.ovpn)
date: 2018-02-02
categories: [System Administrator]
tags: [linux, OpenVPN AS]
---

Most of the command line parameters are executed as root user in the `/usr/local/openvpn_as/scripts/` directory.

Save a user-locked profile to client.ovpn:

`./sacli --user <user> GetUserlogin > client.ovpn`

Save an auto-login type profile to client.ovpn:

`./sacli --user <user> GetAutologin > client.ovpn`