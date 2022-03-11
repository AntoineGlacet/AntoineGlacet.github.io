---
title: "Mount a drive on Ubuntu"
categories:
  - tips
tags:
  - raspberry pi
  - linux server
  - ubuntu
  - media
  - tutorial
header:
  teaser: "assets/images/linux-mount-drive.png"
excerpt: "The good way to mount a drive on a linux server. Perfect for Raspberry Pi ubuntu server."
toc: true
toc_sticky: true
---

![image-center](/{{page.header.teaser}})

# {{page.title}}

Mounting a drive on ubuntu is a rather simple procedure but some important parameters are often forgotten on the tutorials I found online when I first configured my Raspberry Pi as a media server running on docker containers.

## 1. Find your drive details

Many guides online are not really clear on this part so I will try to give the information I wished I had when I started.

<div class="notice--info">

In linux, a **disk** is a physical storage and it can have one or more **partitions**, each formated with its own **filesystem** (eg. ext4, ntfs, vfat, ...). In linux, every **file** and **directory** is under the root directory `/`. Partitions are mounted on directories, usually we mount external storage under `/mnt`.

</div>

To get a good understanding of that, running the command `lsblk` with optional columns can help (`lsblk --help` gets you a list of all available columns).

```sh command codeCopyEnabled
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT,UUID,VENDOR,MODEL,LABEL
```
<div></div>

```sh result (scroll ==> to see details)
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
├─sda1   256M part vfat     /boot/firmware    5C4B-68A0                                                          system-boot
└─sda2 223.3G part ext4     /                 34fa43d2-724c-4b32-81d5-123c567abc12                               writable
sdb      3.7T disk                                                                 StoreJet WDC_WD40NPZZ-00PDPT0
└─sdb1   3.7T part ntfs     /mnt/data         12A34B56C78D9123                                                   Transcend_4TB
```
You should be able to identify your drive easily and write down the FSTYPE and UUID. At this point your drive should not have a mountpoint.

We will use UUID instead of NAME of the partition as the UUID will stay the same between boots or if you use a different usb port.

## 2. Configure mount at boot

Linux uses /etc/fstab (file system table) to determine what and how partitions should be mounted at boot time. We will edit this file to configure our mount.

```sh codeCopyEnabled
sudo nano /etc/fstab
```
<div></div>

add this line at the end of the file (change UUID, File System Type according to previous part).

```sh codeCopyEnabled
# /etc/fstab
# [Device] [Mount Point] [File System Type] [Options] [Dump] [Pass]
UUID=12A34B56C78D9123 /mnt/data ntfs uid=1000,gid=1000,nosuid,nodev,nofail 0 0
```
Then write out with `ctrl+o` and validate with `enter` and exit with `ctrl+x`.
If you want more details about what we wrote up there, expand this next section.

### 3.2 fstab and mount options

<details>
  <summary markdown="1"> Expand section </summary>

  You can find info on the fields with `man fstab` and `man mount`, but here is a summary.

  | item | value | Description |
  | -------- | ---- | ----------- |
  | [Device] | UUID= |  Universally Unique Identifier for the partition to be mounted |
  | [Mount Point] | /mnt/data |  Where the partition will be accessible from in the linux file system tree |
  | [File System Type] |  ntfs | The data structure of the partition we mount |
  | [Options] | uid= | userid for the mount |
  | [Options] | gid= | groupid for the mount |
  | [Options] | nosuid | For security, prevents files on mount to set userid |
  | [Options] | nodev | For security, prevents system from interpreting character or block special devices |
  | [Options] | nofail | allows boot to continue if mount fails |
  | [Dump] | 0 | set to 1 do dump filesystem |
  | [Pass] | 0 | set to 1 if root filesystem |

  The user and group parameters (uid and gid) are very important. When we want to manage access to this directory from docker containers or samba user later on so we will check that it works later on.

</details>

## 3. Mount and verify

You can now enter `mount -a` in the terminal, it looks through /etc/fstab and mount all the devices as described there.
Now that the mount is done, let's check it is there and ownership is set correctly.

```sh command codeCopyEnabled
cd /mnt
ls -l
```

Confirm that you see your userid and groupid for the mounted directory

<div class="notice--info">

**tip:** Should you have any trouble following this guide, have a look at the detailed [linux manual section on mount](https://man7.org/linux/man-pages/man8/mount.8.html), also accessible from terminal with command `man mount`.

</div>
