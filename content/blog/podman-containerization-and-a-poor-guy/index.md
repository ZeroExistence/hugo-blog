---
title: Podman, Containerization, and a Poor Guy
summary: How can you utilize the containerization technology when you want to minimize cost?
date: 2019-08-20

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Victoire Joncheray on Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Tech
  - Podman
---

Deploying Kubernetes, Docker, and cloud-native apps needs large type  of servers, but what if you are a poor guy like me and just want to use  containerization technology on small scale servers? One thing that can  help you is Podman.

[Podman](https://podman.io).  Ahh, what a great daemonless container engine. Daemonless? Yes. It  means that it has minimal footprint on the server, giving much power to  those containers.

We currently use Podman to manage our apps on a 4GB/2vCPU Fedora 30 Linode. As of now, we are comfortably running these apps:

* Reverse-proxy Nginx
* Multiple Django sites
* Tor relay node
* Ipfs node
* Syncthing node
* Development containers

You  might wonder, but running those multiple containers still consume a lot  of resources. This is right, but one other great thing about container  is resource limiting. I just simply limit the resources on all  production containers to 2GB/1vCPU. I did this by running my containers  as a service, then set-up an environment file with this configuration.

```
[root@prod ~]# grep -i EnvironmentFile /etc/systemd/system/cnginx.service
EnvironmentFile=-/etc/sysconfig/podman
[root@prod ~]# cat /etc/sysconfig/podman
PODMAN_OPTIONS=\"--rm -a stdout -a stderr -m 2g --cpus 1\"
```

This is to ensure that no one would hog everything, rendering others  non-responsive. I also set them as a service to automate container  restart if something happens.

```
[root@prod ~]# grep -i restart /etc/systemd/system/cnginx.service
Restart=always
RestartSec=15s",
```

There are instances that podman would kill container due to OOM, but  restore the service by automated restart. If there’s multiple case of  OOM, you will need to adjust the resources accordingly.</p><p>After  using podman for several months now, I just love it. I wish that they  would be able to push their binaries to Debian repository.
