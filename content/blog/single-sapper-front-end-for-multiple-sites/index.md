---
title: Single Sapper Front-End for Multiple Sites
summary: You can create multiple sites with a creative implementation.
date: 2020-12-15

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Brendan Church on Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Tech
  - Svelte
  - Sapper
---

If you have a Django-backend that running multiple sites, you might be thinking of a way to implement a single front-end for those sites. In my case, I am running multiple blog sites with different domains. This blog site is part of that single front-end design with Sapper.

To create this kind of setup, I decided to put all my api endpoints in /api/v1 of my domains, to prevent complications in using CloudFlare and CORS.

* Cloudflare - they only allow 1 depth subdomain for their CDN service.
* CORS - if you haven't setup the CORS header, browsers would not render your api responses.

For this setup, I use an environment variable for easier configuration.

```
export API_ENDPOINT=/api/v1
export API_PROTOCOL=https
```

src/server.js
```
const { PORT, NODE_ENV, API_ENDPOINT, API_PROTOCOL } = process.env;
```

I declare **API_ENDPOINT** to indicate specific url, usually ***/api/v1***, and for API_PROTOCOL, I just use this to indicate if my API_ENDPOINT is using http or https.

There is some logic in determining the apiEndpoint, since in development, I usually use a separate host for back-end.

To add this to middleware session, set this up into your sapper.middleware.

```javascript
        sapper.middleware({
            session: (req, res) => {
                let apiEndpoint
                if (API_ENDPOINT.startsWith('http')) {
                    apiEndpoint = API_ENDPOINT
                }
                else {
                    apiEndpoint = [API_PROTOCOL, '://', req.headers.host, API_ENDPOINT].join('')
                }
                return ({
                    apiEndpoint: apiEndpoint,
                })
            }
        })
```

Once this is setup, the apiEndpoint variable would be available during the preload part of web loading.

This is the code snipplet of one of my Svelte page.

```
<script context="module">
    export async function preload({ params }, session) {
        return this.fetch(`${session.apiEndpoint}/`)
            .then(r => r.json())
            .then(response => {
                return { posts: response };
            });
    }
</script>
```

Using the session.apiEndpoint during fetching instead of hardcording your API Endpoint in your every Sapper deployment would make it available to multiple domains. It is because in production build, it would use the request.headers.host variable. So if someone request the blog.moe.ph page, it would resolve into https://blog.moe.ph/api/v1 endpoint.

With this kind of setup, you can use a single front-end for multiple identical back-end application.
