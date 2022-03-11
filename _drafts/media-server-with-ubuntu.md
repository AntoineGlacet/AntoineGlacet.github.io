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

<div class="notice--info" markdown="1">
 
**Foreword**: This guide simply describes the media server stack I use on my [Raspberry Pi server running on Ubuntu]({% post_url 2022-02-13-rpi-ubuntu-server %}). You can refer directly to my [Github repo](https://github.com/AntoineGlacet/home-server) where all my home server config is stored, including home automation and network tool stacks. A good way to try out is to clone this repo, edit the .env.example file and start docker-compose with `docker-compose --file /path-to-docker-compose.yml --env-file /path-to-.env up -d`.

</div>

## Why Docker
I use Docker to run all the applications of my home servers inside containers. I actually used to just install the applications directly but I chose to migrate to Docker for a few reasons:
- **Keep my system clean:**
Everything from my services and apps are under my `home server` folder and not spread all over.
- **Easy management:**
I can easily install, delete and update all my applications. I could also migrate really easily to another hardware, or a docker-swarm if needed.
- **Learn about Docker:**
Containerisation and docker is really convenient to share and run code and apps on other people's PC or the cloud, super useful skill to pick up.

In terms of performance, there is no difference between running this home server containerised or bare-metal and it saves a lot of time and headaches figuring out compatibilities issues.
## System diagram

![image-center](/assets/images/media-diag.png)
## Configuration

<div class="notice--info">
 
**Prerequisite**: [linux server]({% post_url 2022-02-13-rpi-ubuntu-server %}) with a [mounted drive]({% post_url 2022-02-27-mount-drive-to-linux %})
for data storage, docker and docker-compose.

</div>

If you look at my [Home server repo](https://github.com/AntoineGlacet/home-server), you will notice:

- 3 stacks (home automation / media / tools), divided across 3 directories for clarity
- each container config files are stored in a consistent way, under the stack directory (./config/container-name) 
- a single .env file with environment variables on the root directory to ensure consistency

The only drawback of this organization is we need to tell docker-compose where are the `docker-compose.yml` and `.env` files.
Luckily, it is quite easy with the following command.

```bash codeCopyEnabled
docker-compose --file docker-compose.yml --env-file .env up -d
```

<div class="notice--info">
 
**Notice**: [environment variables](https://docs.docker.com/compose/environment-variables/), are variables that are passed to a container at start time. For variables that need to be kept consistent through all containers (like directory mapping) or that you want to keep secret (like passwords), you can store them in a `.env` file, which docker-compose will read at start time.

</div>


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