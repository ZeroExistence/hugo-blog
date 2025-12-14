---
title: Building Live CD of Debian Testing Repository
summary: Want to live the life in edge? Let's use Debian Testing repository!
date: 2020-01-28

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

Using testing release of Debian as live CD distribution is not as straight forward as building with stable release. The reason for that is there are some steps that should be taken first, before you can use the testing repository. Refer to this link for comprehensive guide for switching from stable to testing release.

https://wiki.debian.org/DebianTesting

These are the steps given in the documentation.

Edit your [apt sources](https://wiki.debian.org/SourcesList/), changing **stable** (or buster, the current codename for stable) to **testing** (or bullseye, the current code name for the next stable release).

Remove, disable or comment out your stable security updates apt sources (anything with ***security.debian.org*** in it).

Remove, disable or comment out any other stable-specific apt sources, like *-backports or *-updates.

Verify that your installation is not fixed to a specific release in **/etc/apt/apt.conf.d/00default-release**.

In short, you must also integrate these requirements on your live-build configuration.

## Step 1

Change the distribution related configuration in the config. To change the required settings, run the commands below. I used the testing release instead of bullseye so that I can still use the configuration once the bullseye release is considered as stable.

```
lb config -d testing;
lb config --debian-installer-distribution testing;
lb config --parent-distribution testing;
lb config --parent-debian-installer-distribution testing;
```

## Step 2

Disable the security updates source.

```
lb config --security false;
```

## Step 3
Disable the backport and updates sources.

```
lb config --backports false;
lb config --updates false;
```

## Step 4
You also need to add the unstable repository on your archive sources, since some packages are not yet available on the testing release repository. You can do that by adding a configuration on config/archives/. Your archives folder should have the files below with corresponding settings. Take note that you should pin the unstable release to lower priority. Setting this incorrectly may cause some issues on the prioritized packages.

```
# cat config/archives/unstable.list.chroot
deb http://deb.debian.org/debian/ unstable main contrib non-free
# cat config/archives/unstable.pref.chroot 
Package: *
Pin: release a=unstable
Pin-Priority: 100
```

Once you completed the give steps above, you should be able to successfully build a live CD from testing release.
