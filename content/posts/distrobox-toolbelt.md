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

# Running GUI Apps from the Host

Perhaps the biggest (and pleasant) surprise of Distrobox is the ability to `export` apps from the container to the host machine. In other words, you can install a command line tool or a GUI app and have the ability to launch it from the host (and in an app launcher in the case of GUI apps with a `.desktop` file) without having to enter the container.

Say I wanted to install Discord, which isn't in the Fedora repos, and for whatever reason I didn't want the Flatpak version. I can install it through the Arch container:

```bash
sudo pacman -S discord
```

I can launch that from within the container using the command:

```bash
discord
```

and it will launch, really cool stuff! But if we want to be able to launch Discord from the host app launcher, we can run the `distrobox-export` command from **within** the container, like so:

```bash
distrobox-export --app discord
```

I was already happy to see this work, but a little icing on the cake, was that launching Discord both from the container and from my app launcher after I exported it had the app indicator working!

# Only the Beginning?

Distrobox is a relatively new program and there are likely to be some bugs / rough edges. I'd only recommend this to "power users" or those who are okay with learning what containers are for the first time. But it's off to a great start, and I'm looking forward to seeing where it goes from here. 

For me, I may experiment with using an immutable OS (such as Fedora Silverblue or OpenSUSE MicroOS) and seeing the experience of using apps from a Distrobox container if I can't find / use the Flatpak version.