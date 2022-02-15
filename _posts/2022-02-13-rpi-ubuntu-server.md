---
title: "Home server with Raspberry Pi"
categories:
  - Home server
tags:
  - server
  - linux
  - media
  - project
  - tutorial
header:
  teaser: assets/images/shinsekai-night.jpg
excerpt: "Tutorial to set up an ubuntu server on RPi. SSD mod included!"
# classes: wide
toc: true
toc_sticky: true
---

![image-center](/{{page.header.teaser}})

# Set up the perfect Raspberry Pi Ubuntu server

<div class="notice">

**Foreword**: This guide simply describe how to set up a performant and reliable Ubuntu server on a Raspberry Pi. For about 50-150 USD you will have a fully functioning server that can be used as a media server, smart home hub, ad blocker, retro gaming station, personal hosting platform, and a huge array of other applications! It will also be a perfect introduction in the fascinating world of open source, IT and DIY.

</div>

## Hardware

### Why a Rpi

You could run an ubuntu server from pretty much anyting, and actually if you have an (relatively) old laptop laying around, it could be a great idea. However, here are a few reasons affordability, tutorials and resources, wide applications (GPIO pins), open source

In fact, I started my home server on a Rpi4, thinking I might upgrade to something beefier someday... This was 2 years ago at the time of writing and this same Rpi still serves me daily!
### List
**Core:**

| Option | Description | Cost |
| ------ | ----------- | ---- |
| Raspberry Pi            | Raspberry Pi 3 and 4 are fine depending on your budget.  | 35-75$ |
| Compatible power supply | Often sold with the Rpi. otherwise see recommendations.  | 0-10$ |
| SD card                 | To store the Raspberry Pi OS, not used after first boot if you do SSD mode (you should).| 10-30$ |
| A computer              | To setup the server. Any computer will do | out of budget |

**Optional:** (recommended for performance and reliability)

| Option | Description | Cost |
| ------ | ----------- | ---- |
| HDD                  | to store large quantity of files and media  | 50-70$ |
| SSD and USB adapter  | the SSD mode is really unleashing the full potential of Rpi as a server. [see below](#ssd-mod)  | 30-40$ |
| powered USB hub      | required to supply extra power for SSD and HDD | 15-20$ |
| case + fan           | At this point, a case and a fan can be useful too | 10-20$ | 

Here is a list of everything you will need to run your ubuntu server with a Raspberry Pi. As we will run headless (= no Graphical User Interface), you do not need any HDMI cable for this guide.

Before buying a SD card or a SSD disk, read on to the [SSD mod](#ssd-mod) section.



### Assembly

![image-center](/assets/raspberrypi-assembly.gif)

### SSD mod
SD cards are not a good solution to run an OS from: they easily get corrupted and have a slower read and write speed compared to a SSD disk. I suggest to start with a SD card you have laying around somewhere (from a camera or an old phone maybe). It will allow you to test things out and you can do the SSD mod after, at which point your Raspberry Pi will boot directly from SSD and will not need this SD card.

little power-on trick
compatibility recommended parts above, further link to chambers blog

## Software

### First boot

let's connect to our instance with SSH. If you are using an older version of Windows, and did not join the cool kid gang using WSL, you will need an extra step here: to install Putty.

It is a good moment to remind you that exploring the world of open source and general geeking will be a far more pleasant experience from a linux environment, and WSL is the painless way to access the full potential of linux from the comfort from Windows

```sh linux codeCopyEnabled
# eg.: ssh ubuntu@192.168.1.85
ssh user@machine
```

```sh macOS codeCopyEnabled
# eg.: ssh ubuntu@192.168.1.85
ssh user@machine
```

```powershell windows>=10 codeCopyEnabled
# eg.: ssh ubuntu@192.168.1.85
ssh user@machine
```

```md windows<10 codeCopyEnabled
# use Putty! :(
```


### Boot from SSD

```console
foo@bar:~$ whoami
foo
```