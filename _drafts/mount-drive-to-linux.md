---
title: "mount a drive on Ubuntu"
categories:
  - tips
tags:
  - linux
  - server
  - tutorial
# header:
#   teaser: "https://raw.githubusercontent.com/manna-harbour/miryoku/master/data/cover/miryoku-kle-cover.png"
excerpt: ""
# classes: wide
---

![image-center](/{{page.header.teaser}})

# {{page.title}}

Mounting a drive on ubuntu is a rather simple procedure but some important parameters are often forgotten on the tutorials I found online when I first configured my Raspberry Pi as a media server running on docker containers.

A short tldr; of what follows:
1. find UUID and file file system type of your drive
2. edit /etc/fstab to mount automatically at boot
3. check correct mount and ownership

## find your drive details

Many guides online are not really clear on this part so I will try to give the information I wished I had when I started.

<div class="notice--info">

In linux, a **disk** is a physical storage and it can have one or more **partitions**, each formated with its own **filesystem** (eg. ext4, ntfs, vfat, ...). In linux, every **file** and **directory** is under the root directory `/`. Partitions are mounted on directories, usually we mount external storage under `/mnt`.

</div>

To get a good understanding of that, running the command `lsblk` with optional columns can help (`lsblk --help` get you a list of all available columns).

```sh command codeCopyEnabled
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,UUID,VENDOR,MODEL,LABEL
```
<div>
</div>



```sh result
NAME     SIZE TYPE FSTYPE   MOUNTPOINT        UUID                                 VENDOR   MODEL                LABEL
loop0     49M loop squashfs /snap/core18/2252
loop1     49M loop squashfs /snap/core18/2289
loop2   57.5M loop squashfs /snap/core20/1274
loop3   57.5M loop squashfs /snap/core20/1332
loop4   60.7M loop squashfs /snap/lxd/21804
loop5   10.6M loop squashfs /snap/nmap/2504
loop6   60.7M loop squashfs /snap/lxd/21843
loop7   10.6M loop squashfs /snap/nmap/2534
loop8   37.7M loop squashfs /snap/snapd/14982
sda    223.6G disk                                                                 SABRENT  SSHD
├─sda1   256M part vfat     /boot/firmware    4D3B-86C0                                                          system-boot
└─sda2 223.3G part ext4     /                 79af43d1-801b-4c28-81d5-724c930bcc83                               writable
sdb      3.7T disk                                                                 StoreJet WDC_WD40NPZZ-00PDPT0
└─sdb1   3.7T part ntfs     /mnt/data         28B67B48B67B1612                                                   Transcend_4TB
```
You should be able to identify your drive easily and write down the FSTYPE and UUID. At this point your drive should not have a mountpoint.

## configure mount at boot

Linux uses /etc/fstab (filesystem table) to 


```sh codeCopyEnabled
# /etc/fstab
# [Device] [Mount Point] [File System Type] [Options] [Dump] [Pass]
UUID=12A34B56C78D9123 /mnt/data ntfs nosuid,uid=1000,gid=1000,nodev,nofail 0 0
```



UUID of the drive will not change




<div class="notice--info">

**tip:** Should you have any trouble following this guide, have a look at the detailed [linux manual section on mount](https://man7.org/linux/man-pages/man8/mount.8.html), also accessible from terminal with command `man mount`.

</div>
