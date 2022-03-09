---
title: "Work around a corporate proxy"
categories:
  - productivity
tags:
  - tutorial
# header:
#   teaser: 
excerpt: "Stay productive behind a corporate proxy"
# classes: wide
toc: true
toc_sticky: true
---

# Work around a corporate proxy

## find the proxy details

linux / win / macOS

## configure

### Windows

### linux / ubuntu / WSL

trick to make a functin in .bashrc

### conda, apt, ubuntu, ...

sudo -E for env variables (sudo is a different user)

VS code git: does not use env variables => .gitconfig



## DNS


1. Turn off generation of /etc/resolv.conf

Using your Linux prompt, (I'm using Ubuntu), modify (or create) /etc/wsl.conf with the following content

[network]
generateResolvConf = false
(Apparently there's a bug in the current release where any trailing whitespace on these lines will trip things up.)

2. Restart the WSL2 Virtual Machine

Exit all of your Linux prompts and run the following Powershell command

wsl --shutdown
3. Create a custom /etc/resolv.conf

Open a new Linux prompt and cd to /etc

If resolv.conf is soft linked to another file, remove the link with

rm resolv.conf
Create a new resolv.conf with the following content

nameserver 1.1.1.1
4. Restart the WSL2 Virtual Machine

Same as step #2


## /etc/hosts