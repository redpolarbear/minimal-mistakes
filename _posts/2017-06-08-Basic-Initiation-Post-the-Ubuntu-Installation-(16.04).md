---
title: Basic Initiation Post the Ubuntu First Installation (16.04)
date: 2017-06-08
categories: [System Administrator]
tags: [linux, network, root, user]
---


## How To Create a Sudo User on Ubuntu
1. Login to the server as the user created during the installation;

2. Use the `adduser` command to add a new user to the system, setup the password and input the necessary information for the new user;

```
# sudo adduser <username>
  [sudo] password for username:

  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully

  Changing the user information for username
  Enter the new value, or press ENTER for the default
    Full Name []:
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
  Is the information correct? [Y/n]
```

3. Use the `usermod` command add the new user to the `sudo` group;

```
# usermod -aG sudo <username>
```

- *By default, on Ubuntu, members of the `sudo` group have sudo privileges.*

#### Refer to the document [How To Create a Sudo User on Ubuntu [Quickstart]](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart)

## How to Enable 'root' Login on Ubuntu
1. Login to the server as the user created during the installation;
2. Use the `passwd` to update the password for the user `root`;

```
# sudo passwd -i root
  Enter new UNIX password:
  Retype new UNIX password:
```

### How to Enable the 'root' Login in SSH
1. Edit the config file (as root) `/etc/ssh/sshd_config`;
2. Find and **comment** the line - `PermitRootLogin prohibit-password` or `PermitRootLogin without-password`;
3. Add one line under the abvoe - `PermitRootLogin yes`;
4. Find and **uncomment** the line - `PasswordAuthentication yes`;
5. Restart or reload the ssh configuration.
```
# sudo service ssh restart
or 
# sudo service ssh reload
```

## Configuare the Network (Static and DHCP)
1. Check Available Network Interfaces on Ubuntu Server 16.04;
```
# sudo ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 00:50:56:8e:ec:95 brd ff:ff:ff:ff:ff:ff
```
- As above the command line shows, the Ubuntu has the Ethernet interfaces called **ens160**. That's the one will be used in the interface setup.

2. Add static IP Configuration to the network configuration file;
- Interface file: ```/etc/network/interfaces```
- Use the comfortable text editor
```
# sudo nano /etc/network/interfaces
```

- Static IP Setup:
```
auto ens160
iface ens160 inet static
  address 192.168.1.10
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

- DHCP Setup:
```
auto ens160
iface ens160 inet dhcp
```

3. Restart Ubuntu Networking Service.

- After setting up IP Configuration, we need to restart Ubuntu networking service.

```
# sudo ip addr flush ens160 && sudo systemctl restart networking.service
```

- Verify the static IP configuration.
```
# sudo ifconfig
or 
# sudo ip add
```

#### Refer to the document [How to set static IP Address in Ubuntu Server 16.04](http://www.configserverfirewall.com/ubuntu-linux/ubuntu-set-static-ip-address/)

