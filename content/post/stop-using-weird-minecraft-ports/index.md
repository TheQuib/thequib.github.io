---
author: "Quinn Henry"
title: "Stop using weird ports for your Minecraft Servers"
date: "2022-05-04"
description: "With MC-Proxy, a reverse proxy for Minecraft servers, you can host all of your servers via a single port."
tags: ["networking", "docker", "containerization"]
categories: ["Docker", "Reverse Proxy"]
image: featuredImage.png
draft: true
---

# The scoop

When I host Minecraft servers for my friends, I seriously hate having to tell them to connect to example.com:25567 or example.com:25568.

Luckily, there is an amazing solution for this: *Reverse Proxies*

&nbsp;

I tried doing this with [Traefik](https://traefik.io/) at first (which is my preferred `http` reverse proxy), but I had zero luck, even after hours of scouring the internet for help.

So as an alternative, I found [mc-router](https://github.com/itzg/mc-router), a reverse proxy for Minecraft, built with [Go](https://go.dev/), in [Docker](https://www.docker.com/).


# What will we be doing?

By the end of this, we will have a working reverse proxy running in a docker container, and two minecraft servers sitting behind `mc-server1.example.com` and `mc-server2.example.com`. Both of these will be accessed using the default minecraft port of `25565`.

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

# Set up DNS

Reverse Proxies rely on DNS to route the incoming connection to the intended server.

Let's say, for this example we are going to proxy 2 servers.