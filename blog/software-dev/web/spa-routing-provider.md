# SPA Routing on Netlify, Vercel, and GitHub

The problem with SPA (Single Page Application) is that there is only one file served (usually `index.html`) and the subsequent routings happen from that file. This means if the URL `https://johndoe.github.io` serves `index.html`, entering `https://johndoe.github.io/category/post` will result in the 404 error because it cannot find `index.html` from that path.

Unless we spin up our own servers, we don't have permission to alter the routing decision. Luckily, some providers provide a way to communicate your routing decision to the server.

## Netlify

Netlify has the [official guide](https://docs.netlify.com/routing/redirects/) for this matter. You can insert the file \_redirect in your publish directory and return status code 200 to every request. The \_redirect snippet below notifies the Netlify server to always return index.62018f6e.css and index.1090ad96.js from my publish directory.

```
/*/index.62018f6e.css /index.62018f6e.css 200
/*/index.1090ad96.js /index.1090ad96.js 200
/* / 200
```

## Vercel

Vercel also has the SPA re-routing [guide](https://vercel.com/docs/configuration). Below is the `vercel.json` configuration file, placed at the root of my project.

```json
{
  "routes": [
    { "handle": "filesystem" },
    { "src": "/(.*)/index.1090ad96.js", "dest": "/index.1090ad96.js" },
    { "src": "/(.*)/index.62018f6e.css", "dest": "/index.62018f6e.css" },
    { "src": "/(.*)", "dest": "/index.html" }
  ]
}
```

## GitHub Pages

GitHub Pages is not as SPA-friendly as the previous twos so it is less straightforward. This [hack](https://github.com/rafgraph/spa-github-pages) inserts a script into a 404 response that will redirect the browser to the desired location. Have fun!
