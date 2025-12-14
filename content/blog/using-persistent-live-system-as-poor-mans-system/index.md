---
title: Using Persistent Live System as Poor Man's System
summary: If you don't have a harddisk, use a live system with flash drive for cost-effective storage system.
date: 2019-11-16

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Markus Spiske on Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Tech
  - Debian
  - Live-system
---

I have a weird setup of my work station. I am currently using a system with damaged SATA boards, so I search for a way to use a diskless work station. It's not entirely diskless, since I'm now using a flashdrive with live Debian.

If you are interested, here is the link for the base configuration of my live system.

[ZeroExistence/debian-live-workstation](https://github.com/ZeroExistence/debian-live-workstation)

## Building

The image will be built by using container. Use the Dockerfile to generate a container for live build. Buildah is used in creating the container image.

```
buildah bud -f Dockerfile -t debian-live .
```

Run the container using podman. The container needs privilaged access to the host system. You also need to use persistent storage for the image file, with exec,dev flags.

```
podman run -ti --privileged --name debian-live -v vdebian:/app:exec,dev debian-live
```

Go to /app directory. Clone this repository, and go inside the repository's directory.

```
cd /app;
git clone https://github.com/ZeroExistence/debian-live-workstation.git;
cd debian-live-workstation;
```

Run the live build command.

```
lb build
```
