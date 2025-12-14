---
title: King Traefik 2.0, Queen Django, and Jack passHostHeader
summary: How to not waste your time in passHostHeader setting in Traefik 2.0 as reverse proxy to Django apps.
date: 2019-08-30

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Tech
  - Podman
  - Traefik
---

Deploying Django application means deploying it behind Nginx webserver most of the time. We are also using this setup. Deploying it this way is easy, since you need to set <i>proxy_pass</i> and <i>proxy_set_header</i>.

The only problem with this setup is when you only have 1 Nginx webserver for multiple Django backend. When you deploy new Django apps, you need to add new setting for it and restart the server. You might be able to reload it by sending SIGHUP to Nginx master process, but we are using container, so it's hassle. You also need to reload it every time you edited something.

That’s when we look into alternative reverse proxy. We look for more cloud-native proxy, and that’s when we found about Traefik. Traefik is a cloud native edge router. It has integrations into multiple cluster technology. It also supports live reload of configuration files for dynamic settings. We are using podman to manage containers, so we are utilizing the dynamic file reload.

We start playing with Traefik, and use version 2. Tinkering with Traefik is not that easy if you’re not familiar with the earlier version.

The setting that really ticked our testing is passing the proxy host header to backend. The first settings that we look through are middleware headers settings of Traefik. This is the most logical place to look first, if you are playing with headers, right? We played with these settings.

Seems okay, but it didn’t actually work. The host header is still not pass to the backend. I hold some grudge to Traefik this time, then I realized that I need to check the settings for earlier version. There are Traefik settings scattered through Github, so I found this boolean setting.

```
passHostHeader = true
```

I guess this setting should be present to Traefik 2.0. After checking the dynamic configuration reference, I found this setting.

```
services:
  Service01:
    loadBalancer:
      passHostHeader: true
```

I set this to true and the backend is now responding.

I guess everyone should know about this setting.