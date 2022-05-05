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

------

# Requirements

 - A virtual machine with Ubuntu 22.04 or later
   - Or bare metal, I won't judge
 - A bit of knowledge in the ways of Docker
