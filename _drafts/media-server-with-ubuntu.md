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
 
**Notice**: [environment variables](https://docs.docker.com/compose/environment-variables/), are variables that are passed to a container at start time. For variables that need to be kept consistent through all containers (like directory mapping) or that you want to keep secret (like passwords), you may want to store them in a separate `.env` file. At start time, docker-compose will read the .env file and assign required environment variables.

</div>

## Media stack

In this blog post, we will only look at the media stack. You can refer to the [`docker-compose.yml`](https://github.com/AntoineGlacet/home-server/blob/master/media/docker-compose.yml) for the configuration. We will review briefly each container, but it is basically 6 images from [linuxserver.io](https://www.linuxserver.io/) with simple configuration, and a more complex [haugene/transmission](https://haugene.github.io/docker-transmission-openvpn/) image which I will explain below.


### Plex

```yml codeCopyEnabled
  # plex media server
  plex:
    # https://hub.docker.com/r/linuxserver/plex
    image: lscr.io/linuxserver/plex
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

### Radaar

```yml codeCopyEnabled
  # movie library
  radaar:
    # https://hub.docker.com/r/linuxserver/radarr
    image: lscr.io/linuxserver/radarr
    container_name: radarr
    hostname: radarr
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - ./config/radarr:/config
      - ${DATA}:/data
    ports:
      - 7878:7878
    depends_on:
      - jackett
      - transmission
    restart: unless-stopped

```

### Sonaar

```yml codeCopyEnabled
  # TV show library
  sonarr:
    # https://hub.docker.com/r/linuxserver/sonarr
    image: lscr.io/linuxserver/sonarr
    container_name: sonarr
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - ./config/sonarr:/config
      - ${DATA}:/data
    ports:
      - 8989:8989
    depends_on:
      - jackett
      - transmission
    restart: unless-stopped

```

### Jackett

```yml codeCopyEnabled
  # torrent tracker search
  jackett:
    # https://hub.docker.com/r/linuxserver/jackett
    image: lscr.io/linuxserver/jackett
    container_name: jackett
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - ./config/jackett:/config
      - ${DOWNLOADS}:/downloads
    ports:
      - 9117:9117
    restart: unless-stopped

```

### Calibre

```yml codeCopyEnabled
  # ebooks library manager and server (include webserver)
  calibre:
    # https://hub.docker.com/r/linuxserver/calibre
    image: lscr.io/linuxserver/calibre:arm64v8-arch
    container_name: calibre
    restart: unless-stopped
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
    volumes:
      - ./config/calibre:/config
      - ${DATA}:/data
    ports:
      - 8081:8081/tcp # Calibre webserver gui.
      - 8080:8080/tcp # Calibre desktop gui.

```
### Transmission-openvpn

Here is the tricky part of this stack. This container make sure that all your torrents are downloaded via a VPN. This is for privacy reasons, if you do not care about that, or do not want to subscribe to a paying VPN, you can use the much simpler [linuxserver image for transmission](https://hub.docker.com/r/linuxserver/transmission). Personally, I use a VPN to access geography-locked content and services so I was already a subscriber.

I will not go much into detail here, but follow scrupulously the config values. Please have a look at the [`.env`](https://github.com/AntoineGlacet/home-server/blob/master/.env.example) example file for reference.

```yml codeCopyEnabled
  # torrent client
  # special image bundled with VPN
  transmission:
    # https://haugene.github.io/docker-transmission-openvpn/
    image: haugene/transmission-openvpn
    container_name: transmission
    volumes:
      - ./config/transmission:/config
      - ${DATA}:/data
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=${TZ}
      # VPN and networking https://haugene.github.io/docker-transmission-openvpn/provider-specific/
      - CREATE_TUN_DEVICE=true
      - OPENVPN_PROVIDER=NORDVPN
      - NORDVPN_COUNTRY=${NORDVPN_COUNTRY}
      - NORDVPN_CATEGORY=${NORDVPN_CATEGORY}
      - NORDVPN_PROTOCOL=tcp
      - OPENVPN_USERNAME=${OPENVPN_USERNAME}
      - OPENVPN_PASSWORD=${OPENVPN_PASSWORD}
      - WEBPROXY_ENABLED=false
      - LOCAL_NETWORK=${LOCAL_NETWORK}
      - DISABLE_PORT_UPDATER=true
      # Transmission config
      - TRANSMISSION_HOME=/config
      - TRANSMISSION_DOWNLOAD_DIR=/data/downloads
      - TRANSMISSION_IDLE_SEEDING_LIMIT_ENABLED=true
      - TRANSMISSION_SEED_QUEUE_ENABLED=true
      - TRANSMISSION_INCOMPLETE_DIR_ENABLED=false
      - TRANSMISSION_DOWNLOAD_QUEUE_SIZE=30
      - TRANSMISSION_WEB_UI=combustion
    cap_add:
      - NET_ADMIN
    logging:
      driver: json-file
      options:
        max-size: 10m
    ports:
      - "9091:9091"
    restart: unless-stopped
```