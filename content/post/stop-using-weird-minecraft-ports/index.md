---
author: "Quinn Henry"
title: "Stop using weird ports for your Minecraft Servers"
date: "2022-05-04"
description: "With Infrared, a reverse proxy for Minecraft servers, you can host all of your servers via a single port."
tags: ["networking", "docker", "containerization"]
categories: ["Docker", "Reverse Proxy"]
image: featuredImage.png
---

# The scoop

When I host Minecraft servers for my friends, I seriously hate having to tell them to connect to `example.com:25567` or `example.com:25568`.

Luckily, there is an amazing solution for this: *Reverse Proxies*

&nbsp;

I tried doing this with [Traefik](https://traefik.io/) at first (which is my preferred `http` reverse proxy), but I had zero luck, even after hours of scouring the internet for help.

So as an alternative, I found [infrared](https://github.com/haveachin/infrared), a reverse proxy for Minecraft by [Hendrik Schlehlein](https://github.com/haveachin), built with [Go](https://go.dev/).


# What will we be doing?

By the end of this, we will have a working reverse proxy running in a docker container, and two minecraft servers sitting behind `mc-server1.example.com` and `mc-server2.example.com`. Both of these will be accessed using the default minecraft port of `25565`.


# Example files

If you want to just view some example files, check out my [Overture](https://github.com/TheQuib/overture) repository, under the `Docker/Infrared` directory.

------

# Before You Start

## Requirements

 - A virtual machine with Ubuntu 22.04 or later
   - Or bare metal, I won't judge
 - A bit of knowledge in the ways of `Docker`
   - i.e. you will need `Docker` installed on your server
 - At least 2 working Minecraft servers
 - Ability to edit DNS records

## Optional

 - Ability to port forward on your router (if you want to access outside of your network)

------

# Set up Proxy Server

## Create Proxy Server

To get it all started, we'll need a server that will act as a proxy.
 - This can be a new virtual machine, or existing. It just needs to have `Docker` installed
 - Make sure you take note of its IP Address
   - For this example, we'll use `192.168.1.10`

## Create necessary files and directories

`cd` to a directory that you want to store your files in, your current user's `home` directory is probably best

Create the necessary directories and files:

```bash
mkdir infrared
touch docker-compose.yml
mkdir data
cd data
mkdir configs
cd configs
touch mc-server1.example.com.json
touch mc-server2.example.com.json
cd ../../
```

### What these commands do

 - Create a project folder
 - Create `docker-compose` file to deploy from
 - Creates a `data/configs` directory to store configurations
 - Creates two configuration files, one for each server name listed under [What will we be doing?](#what-will-we-be-doing)


## Edit docker-compose.yml

Now, you'll want to edit the `docker-compose.yml` file:

```bash
nano docker-compose.yml
```

And paste in the following text:

```yaml
version: '3'

services:
  infrared:
    image: ghcr.io/haveachin/infrared:1.3.3
    container_name: infrared
    restart: unless-stopped
    stdin_open: true
    tty: true
    ports:
      - "25565:25565/tcp"
    volumes:
      - "./data/configs:/configs"
    expose:
      - "25565"
    environment:
      INFRARED_CONFIG_PATH: "/configs"
```

### What this bit of Yaml does

 - Uses docker-compose version 3
 - Creates a service called `infrared`
   - With the image `ghcr.io/haveachin/infrared:1.3.3`
   - With a name of `infrared`
   - Don't restart the container unless we (the admin) stops it
   - Open up a port
   - Define a location for configurations


## Edit configurations

Now, go into the configurations directory

```bash
cd /data/configs
```

You may want to change the names of the created files to match the `FQDN`'s of  your actual servers.

I will continue to use the names `mc-server1.example.com` and `mc-server2.example.com` for this tutorial, however.

Edit each file and include the following contents:

### `mc-server.example.com.json`

```json
{
    "domainName": "mc-server1.example.com",
    "proxyTo": "192.168.0.11:25565",
    "listenTo": "0.0.0.0:25565",
    "disconnectMessage": "Goodbye",
    "offlineStatus": {
      "motd": "Server is currently offline :("
    }
}
```

### `mc-server2.example.com.json`

```json
{
    "domainName": "mc-server2.example.com",
    "proxyTo": "192.168.0.12:25565",
    "listenTo": "0.0.0.0:25565",
    "disconnectMessage": "Goodbye",
    "offlineStatus": {
      "motd": "Server is currently offline :("
    }
}
```

### Configuration note

Make sure that you change `domainName` to your server's `FQDN` and the `proxyTo` address and port to match your Minecraft server.


------

# Set up DNS

Reverse Proxies rely on DNS to route the incoming connection to the intended server.

Let's say, for this example we are going to proxy 2 servers:
  - `mc-server1.example.com`
  - `mc-server2.example.com`

On your DNS server, create an A record for each of the servers, pointed at your proxy server. For eaxmple:
  - `A`: `mc-server1.example.com` > `192.168.1.10`
  - `A`: `mc-server2.example.com` > `192.168.1.10`

------

# Test 'er out

## Spin up your container

Now you're ready to spin up your container!

Make sure you're in the same directory as your `docker-compose.yml` file, and run:

```bash
sudo docker-compose up -d
```

## Launch Minecraft, and check your work

Now, launch Minecraft, head over to the multiplayer menu, and add servers that point to the `FQDN`s for your A records created in [Set up DNS](#set-up-dns).

If you did everything correctly, these should be showing as online! (If not make sure they're running first 😉)