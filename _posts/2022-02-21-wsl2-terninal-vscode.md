---
title: "WSL2+Windows Terminal+VScode"
categories:
  - productivity
tags:
  - linux
  - tutorial
  - coding
excerpt: "Ultimate coding and geeking environment on Windows"
header:
  teaser: assets/images/wsl-terminal-vscode.png
# classes: wide
toc: true
toc_sticky: true
---
![image-center](/{{page.header.teaser}})

# Windows coding environment

<div class="notice--info">

**Foreword:** Windows is king at work and in gamers' homes, and now it comes with everything you need to code and use linux seamlessly. It is so simple to set up and use that Windows might have actually become linux's best friend! 

</div>

There is no way around it, linux is a must to be able to code and geek. The best coding environment on Windows is just to use linux on Windows, without all the hassle and complexity it used to be.

The core of the environment is WSL - Windows Subsystem for Linux, which runs linux on Windows. To interact with WSL, Windows Terminal and VScode, respectively a terminal and an IDE (Integrated Development Environment) are both free, open source and integrate very well with WSL. Oh, and they are also top of the class in their respective category. I have yet to find something that these 3 softwares cannot achieve.

<div class="notice--warning">

**Notice:** You must be running Windows 10 version 2004 and higher (Build 19041 and higher) or Windows 11.

</div>

## Windows Terminal

Windows Terminal is a simple, customizable terminal that can be used with Powershell, WSL and any application with a command line interface. Basically, that's where we type commands.

Install Windows Terminal through Microsoft store following this [link](https://aka.ms/terminal).

## WSL2

WSL - Windows Subsystem for Linux, is a linux environment running natively on Windows. It will let you pick a linux distribution (distro), and run it without exiting your Windows session. From WSL, you will be able to do anything a linux system can do ... and more! For example: you can access your Windows files from your WSL and vice-versa.

### Install
It has never been that easy to install WSL! Just open a PowerShell prompt (in Windows Terminal!) and type the following.

```powershell PowerShell codeCopyEnabled
# set default wsl version
wsl --set-default-version 2
# check which distro you can install
wsl --list --online
# install your favorite distro
wsl --install -d <DistroName> 
```
```powershell Example codeCopyEnabled
wsl --install -d Ubuntu-20.04
```

After that, make sure you are running the version 2 of WSL by entering the command `wsl -l -v` (still in PowerShell). If you see version 1, upgrade to 2 with command `wsl --set-version <distro name> 2` (so, `wsl --set-version Ubuntu-20.04 2` in our example)


### Start linux
Start Windows Terminal and the new WSL distro will now be available. At first boot, you will be asked to create a username and password for it, it will be your default user for this distro and will be granted admin privileges, allowing to run `sudo` (Super User Do) administrative commands.


### Move files (Optional)

<details>
  <summary markdown="1"> Expand section </summary>

  WSL file system is a .vhdx file that Windows will store on your C drive by default. If you want to move that somewhere else, what you need to do is actually export your distro somewhere and reimport it from there.

  1. confirm your username (the one you choose at [first start](#start-linux) of your distro) by running command `whoami` in your distro

  > From here, everything happens on PowerShell, replace Ubuntu-20.04 by your distro name (`wsl -l`)

  2. export your distro (this takes time)

  ```powershell codeCopyEnabled
  mkdir D:\temp # we will delete this at the end
  wsl --export Ubuntu-20.04 D:\WSL\Ubuntu-20.04.tar
  ```
  3. delete your existing distro with `wsl --unregister Ubuntu`

  4. reimport your distro to the new location (this also takes time)

  ```powershell codeCopyEnabled
  mkdir D:\WSL
  wsl --import Ubuntu-20.04 D:\WSL\ D:\temp\Ubuntu-20.04.tar
  ```

  5. switch default user to you

  ```powershell codeCopyEnabled
  cd $env:USERPROFILE\AppData\Local\Microsoft\WindowsApps
  ubuntu config --default-user your-username
  ```
  restart and it's done!
</details>

## VScode

VScode is the free and open source code editor developed by Microsoft, it can handle any languages and have a huge array of extensions maintained by the open source community. It includes support for debugging, syntax highlighting, intelligent code completion, snippets, code refactoring, and embedded Git... and pretty much anything you would want in a code editor.

Download and run [VS code installer](https://go.microsoft.com/fwlink/?LinkID=534107), choose to install for user, except if you want multiple users to have access to it.

Once installed, have a look at the extensions menu and install [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) to access WSL from VScode.

## Windows Insider Program (Optional)



<details>
  <summary markdown="1"> Expand section </summary>

  ![image-center](/assets/images/Windows_Insider_Ninjacat_Trex.jpg)

  <div class="notice--warning">

  **Notice:** This step is totally optional as it will have significant impact on your whole PC and not just your coding experience.

  </div>

  Joining the Windows Insider Program lets you receive the new updates and features of Windows early, it is a really cool thing if you want to have the latest improvements for all the things described in this blog post. It also means that you will be bumped up to the latest build of Windows 11 automatically and some things might be a little more unstable there.

  Personally, I chose to join the Insider Program on their most advanced channel (dev) to receive the most up to date build of Windows. I honestly did not notice any bugs or instability and it has allowed me to use the latest improvements of WSL, like running GUI apps from WSL natively. This is so great that I would not go back.

  Once again, the choice is up to you, maybe something to consider at a later stage if you end up needing some unreleased features.

  For the registration process, you can refer to [MS official doc](https://insider.windows.com/en-us/getting-started)
</details>

## Bonus: theme!

![image](https://draculatheme.com/static/img/screenshots/visual-studio-code.png)

A cool theme is always nice set the mood. Both Windows Terminal and VScode are customizable so do not hesitate to play around with themes. Personally, I am using the [dracula theme](https://draculatheme.com/). I love it as it can be set for most the apps I use daily and I even got the matching keycap set!

<div class="notice--info">

**tip:** Should you have any trouble following this guide, have a look at the detailed [Windows Terminal docs](https://docs.microsoft.com/en-us/windows/terminal/install), [WSL docs](https://docs.microsoft.com/en-us/windows/wsl/install) and the [VScode docs](https://code.visualstudio.com/docs/setup/windows).

</div>
