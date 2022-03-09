---
title: "media server with Ubuntu"
categories:
  - home server
tags:
  - raspberry pi
  - linux server
  - docker
  - ubuntu
  - media
  - tutorial
header:
  teaser: "assets/images/media-server.png"
excerpt: "How to set up a media server on Raspberry Pi with Docker (plex, radarr, sonarr, haugene/transmission-openvpn, calibre)"
# classes: wide
toc: true
toc_sticky: true
---

![image-center](/{{page.header.teaser}})
# Ubuntu Docker media server

<div class="notice--info">
 
**Foreword**: This guide simply describes the media server stack I use on my [Raspberry Pi server running on Ubuntu]({% post_url 2022-02-13-rpi-ubuntu-server %}). You can refer directly to my [Github repo](https://github.com/AntoineGlacet/home-server) where all my home server config is stored, including home automation and network tool stacks. A good way to try out is to clone this repo, edit the .env.example file and start docker-compose with `docker-compose --file /path-to-docker-compose.yml --env-file /path-to-.env up -d`.

</div>

## Why Docker
I use Docker to run all the applications of my home servers inside containers. I actually used to just install the applications directly but I chose to migrate to Docker for a few reasons:
- **Keep my system clean:**
Everything from my services and apps are under my `home server` folder and not spread into my linux installation.
- **Easy management:**
I can easily install, delete and update all my applications. I could also migrate really easily to another hardware if needed.
- **Learn about Docker:**
I since used Docker to run some things I use at work on servers, thanks to the know-how I build on my home server

In terms of performance, there is no difference between running this home server with Docker or directly on ubuntu and it saves a lot of time and headaches figuring out compatibilities issues.
## System diagram

![image-center](/assets/images/media-diag.png)
## Configuration

<div class="notice--info">
 
**Prerequisite**: linux server with a mounted drive for data storage, docker and docker-compose.

</div>

If you look at my [Home server repo](https://github.com/AntoineGlacet/home-server), you will see that the 

- split the docker into main functions for clarity (home automation / media / tools)
- keep each container persistent config data in a consistent way (./config/container-name) 
- have a single .env file with password and common config to avoid mistakes

The only drawback of this organization is we need to tell docker-compose how to 

```
docker-compose --file docker-compose.yml --env-file .env up -d
```

### plex

```md codeCopyEnabled
  plex:
    image: ghcr.io/linuxserver/plex
    container_name: plex
    network_mode: host
    volumes:
      - ./config/plex:/config
      - ${MEDIA}:/media
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      - version=docker
    restart: unless-stopped

```

### radaar

### sonaar

### jackett

### openvpn-transmission

### bonus: calibre and calibre-web