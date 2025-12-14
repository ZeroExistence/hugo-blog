---
title: Site Redirection in Traefik using RedirectRegex Middleware
summary: Implementing regex redirect in Traefik is not as simple as you think.
date: 2019-11-06

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Brendan Church on Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Tech
  - Traefik
---

Implementing URL redirection in Traefik works like normal service with middleware processing. To setup redirection, setup the base router with middleware, and some dummy service.

```
http:
  routers:
    shop-secure:
      entryPoints:
      - "web-fe-secure"
      rule: "Host(`shop.moe.ph`)"
      middlewares:
      - "shopredirect"
      service: "shop-redirect"
      tls: {}
```

For middleware, you will need to use redirectRegex middleware processor. Just put the URL that you want to process, and it's replacement.

```
http:
  middlewares:
    shopredirect:
      redirectRegex:
        regex: "^https://shop.moe.ph/(.*)"
        replacement: "https://shopee.ph/moe_ph"
```

Unfortunately, this router-middleware processing of URL redirection still needs service block. If you are redirecting your URL to another domain, just put a dummy service.

```
http:
  services:
    shop-redirect:
      loadBalancer:
        servers:
        - url: "http://localhost/"
```

To do more complex redirection, just check the Traefik's [RedirectRegex](https://docs.traefik.io/middlewares/redirectregex/) documentation.
