---
title: "Distrobox - A great item in the Linux toolbelt"
date: 2021-12-23T01:11:39-05:00
description: "This post discusses my use case for Distrobox; and a brief comparison with Toolbox."
draft: false
---

# What is Distrobox?

Per their [Github Page](https://github.com/89luca89/distrobox), Distrobox is a way to "use any Linux distribution inside your terminal" using either Podman or Docker. In a sense, Distrobox is a simple front-end for Podman/Docker to easily create containers using any Linux environment of your choosing (as long as that environment has a container image), and providing some nice OOTB features such as being able to launch graphical apps, access external USB devices, etc. This is possible without Distrobox, but it does make it a lot simpler.

# Containerization

Containerization, from the perspective of a developer on their local machine, provides a great benefit in the ability
to create separate "environments" from their base system where they can tinker with new languages, libraries, etc, without the fear of potentially breaking their base system in some way. 

On Fedora systems, there is a handy tool called [Toolbox](https://github.com/containers/toolbox), so why not use Toolbox instead (since I am currently using Fedora)?

# So, How is Distrobox Different?

At the risk of over-simplifying Distrobox, from my perspective, it does two things differently from any other solution right now;

1. Distrobox is a series of POSIX shell scripts, making compatibility a primary feature, whereas Toolbox is a Go program that is geared toward Fedora systems.

2. One of Distrobox's primary goals it to support the containerization of "any" Linux environment. 

I'm going to focus on and discuss #2 in the above list.

# My Personal Use Case

I love Fedora, and made it my distribution of choice on my new Framework laptop (post coming soon on it?). However, despite its bleeding-edge approach to packages, there were two packages that weren't quite up to date as much as I'd like:

1. **Hugo** (which is what I used to build this site), which at the time of writing, was on version '0.80' making it ~10 versions behind the latest. 
2. **Go**. Fedora has the latest patches for version 1.16, however at the time of writing, 1.17 is the latest version and not available in the Fedora 35 repos. 

As it turns out, using a container is a perfect way to solve this. But see the problem with Toolbox in this scenario? All I'd be doing is replicating a Fedora environment in a Toolbox container (and thus, using the 'older' versions I don't want to use), so that wouldn't help me out much.

# Distrobox to the Rescue

As you may have guessed based on the purpose of this post, Distrobox satisfied my use case beautifully. What I decided to do was to spin up an Arch Linux container in Distrobox on my Fedora system. The Arch repos had the complete updated versions for both Hugo and Go.

So, for the sake of brevity, I installed Distrobox using [its installation instructions](https://github.com/89luca89/distrobox#installation). 

Then, I used Podman to pull down the latest `archlinux` image:

```bash
podman pull archlinux # then selected the latest image from docker.io
```

Now we can create the container using Distrobox with the `distrobox-create` command:

```bash
distrobox-create --image archlinux --name dev
```

To break things down a bit in this case, the `distrobox-create` command created a new container named `dev` using the `archlinux` image I pulled using podman earlier. 

And then I was able to enter into the container using the `distrobox-enter` command: 

```bash
distrobox-enter --name dev
```

And voila, that terminal session will be entered into the `dev` container. I was able to use pacman to install Hugo / Go, and continue on my merry-way.

# WSL

As a new developer who is still trying to figure out shortcuts / new ways of tackling issues in my workflow, I want to give a shout-out to WSL for making me aware of how this containerized approach can help me. Specifically, WSL 2 with its GUI support allowed me to install Firefox using an Arch image in WSL, and it showed up in the start menu without an issue. I don't have many practical uses for this, but it was very cool to see work!

I do not prefer using Windows as a development machine, but this was a workflow addition I was happy to see Distrobox make easy on Linux, and WSL made me aware of it. 

As a side note, I'm curious on whether it'd be possible for GUI applications in Distrobox to show up as a desktop application on the base system. As a Fedora user, I could see the benefit of installing a lesser-known GUI application from the AUR, but be able to easily access it from my search menu, as if it was natively installed. 