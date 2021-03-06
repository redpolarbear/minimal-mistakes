---
title: P2V - Convert Linux with RAID to VM
date: 2017-07-20
categories: [System Administrator]
tags: [linux, vmware]
---

## Problem
I had one old physical server which was still running a production system. One day it reported the error that the storage was almost full and after the check, there was not much space I could released and the hard disk can't be expanded. To re-install the whole system on another hardware platform or to be virtualized from scratch seems not likely a good idea. Because the version of the system was a community version and wasn't supported by the official now, it's pretty hard to find more support document online for this software.


## Solution
The straight forward and simple solution will be virtualized the physical machine with its current configuration and the contents.

```
Source OS:  CentOS 5
Tool:       VMware vCenter Converter Standalone client v6.1.1
```

## Steps

### Step 1.  VMware vCenter Converter: 
1.  "Convert Machine" -> Powered on "Remote Linux Machine" -> Input the username (root) and the password -> Choose the destination location - VMware ESXi server (or vCenter) with the username and the password.

2.  The process is well-described and easy to go. For this case, I changed the size of the partition in the destination to solve the problem. Note: Please remember the size of each partition (swap, boot, root, etc.) that will help me to change the `fstab` and `grub`.
    
3.  Click the "Finish" to submit the convert request and kick off the operation. **IMPORTANT:** Because in the original system the disk was set up as *RAID1*, the VMware Converter will occur an error at the last around 97% - ***Partition number must be set for the boot volume***. It's OK and will be fixed in the next step.
    
### Step 2. Manually modify the file system in the new VM:
As I mentioned above, I need to manually set up the volumes in the new VM. After the conversion, the new VM has 4 partitions as below

```js
#fdisk -l
/dev/sda1   //the /boot in the original OS
/dev/sdb1   //the / in the original OS
/dev/sdc1   //the swap parition in the original OS
/dev/sdd1   //the swap parition in the original OS
```

- Boot up from the CentOS 5 installation media and enter the *rescue mode* by inputting `linux rescue` on the startup screen. Again, because of the reason above, the rescue mode will fail to find the current OS in the disk. It's OK. Leave it for a while and will be fixed in the next steps.

- Re-mount the disks and change the `fstab` and `grub`, because the *RAID* has been broken after the conversion but the `fstab` and the `grub` still contained and pointed to the *RAID* disk in the original system configuration.

```js
#mkdir /mnt/root

#mount /dev/sdb1 /mnt/root

#chroot /mnt/root
```

- Change the `fstab` and reboot

```js
#cd /etc
#vi fstab
//Change the original /dev/md0 (/boot) to the current /dev/sda1.
//Change the original /dev/md1 (/) to the current /dev/sdb1.
//Change the swap parition to the current /dev/sdc1 and /dev/sdd1.
//Save and exit
//Reboot
```

- Change the `grub` and mark the boot flag for the boot partition.

```js
#cd /boot/grub
#vi grub.conf
//Change the original 'root=/dev/md1' to the current 'root=/dev/sdb1'.
//Append the parameter - 'nodmraid' to the 'kernel' line.
//Save and exit

#fdisk /dev/sda
//Press 'a' to set the boot flag.
//Press '1' to set the number of the boot partition
//Press 'w' to save and exit
#fdisk -l
//Verify that there is one '*' flag on Boot for /dev/sda1

//Reboot
```

- Reboot from live CD again and enter the *rescue* mode again. The startup wizard could successfully mount the original system in the `/mnt/sysimage`.

```js
#mount --bind /proc /mnt/sysimage/proc
#mount --bind /dev /mnt/sysimage/dev
#mount --bind /sys /mnt/sysimage/sys
#chroot /mnt/sysimage
```

- Re-install the GRUB

```js
#grub-install /dev/sda
```

- Create the new *initrd* by `mkinitrd`.

```js
#cd /boot
#cp -p /boot/initrd-$(uname -r).img /boot/initrd-$(uname -r).img.bak //Create a backup copy of the current initrd.
#mkinitrd -f -v /boot/initrd-$(uname -r).img $(uname -r) //Now create the initrd for the current kernel.
//if the mkinitrd failed, try the other verion of the kernel which was existed in the /boot.
```

- Eject the ISO file and reboot the VM from the local HDD.

Till now, all the necessary operation has been completed and the system could be boot up as before. All the service will be running and up in the VM.

Note: Need to pay attention to the IP address of the VM and make sure it won't be the same and conflict to the physical machine.

