# Budget-Tracker

Using Progressive Web Application (PWA) this application enables the user to add expenses and deposits to their budget with or without an online connection. When entering transactions offline, data should populate the total when connected back online.

### User Story
AS AN avid traveller
I WANT to be able to track my withdrawals and deposits with or without a data/internet connection
SO THAT my account balance is accurate when I am traveling

### Business Context
Giving users a fast and easy way to track their money is important, but allowing them to access that information anytime is even more important. Having offline functionality is paramount to our applications success.

# General Info

### What is Progressive Web Application (PWA)?
- A Progressive Web App (PWA) is a web app that uses modern web capabilities to deliver an app-like experience to users. These apps meet certain requirements, are deployed to servers, accessible through URLs, and indexed by search engines.

#### Offline Support
- Apps should be able to work offline. Whether that be displaying a proper "offline" message or caching app data for display purpose.

# Functionality 

BUDGET-TRACKER Offline Functionality:
- Enter deposits offline
- Enter expenses offline

When brought back online:
- Offline entries should be added to tracker.

## *In order to achieve OFFLINE SUPPORT, a manifest file and a service worker file must be created.*

### Manifest ``` manifest.webmanifest ```
- #### Purpose
    - The manifest file is a simple text file (JSON file) that lists the resources (app's displayed name, icons, as well as splash screen) the browser should cache for offline access. 
``` bash
{
    "short_name": "Budget-Tracker",
    "name": "Budget-Tracker",
    "icons": [
        {
            "src": "/img/icons/money.png",
            "sizes": "192x192",
            "type": "image/png"
        }
    ],
    "start_url": "/",
    "background_color": "#ffffff",
    "display": "standalone",
    "theme_color": "#ffffff"
}

```

This can be linked in the main html file ``` index.html ```
``` bash
<link rel="manifest" href="/manifest.webmanifest" />

```
### Service Worker  ``` service-worker.js ```
- #### Purpose
    - Service worker provides a programmatic way to cache app resources.  

- #### Service Worker Life Cycle
    - install
    - activate
    - fetch

``` bash 
console.log('This is your service-worker.js file!');

const FILES_TO_CACHE = [
    `/db.js`,
    `/index.html`,
    `/index.js`,
    `/index.css`,
    `/manifest.webmanifest`,
    `/img/icons/money.png`
];

const STATIC_CACHE = `static-cache-v1`;
const RUNTIME_CACHE = `runtime-cache`;

self.addEventListener(`install`, event => {
    event.waitUntil(
        caches
            .open(STATIC_CACHE)
            .then(cache => cache.addAll(FILES_TO_CACHE))
            .then(() => self.skipWaiting())
    );
});

self.addEventListener(`activate`, event => {
    const currentCaches = [STATIC_CACHE, RUNTIME_CACHE];
    event.waitUntil(
        caches
            .keys()
            .then(cacheNames =>
                // return array of cache names that are old to delete
                cacheNames.filter(cacheName => !currentCaches.includes(cacheName))
            )
            .then(cachesToDelete =>
                Promise.all(
                    cachesToDelete.map(cacheToDelete => caches.delete(cacheToDelete))
                )
            )
            .then(() => self.clients.claim())
    );
});

self.addEventListener(`fetch`, event => {
    if (
        event.request.method !== `GET` ||
        !event.request.url.startsWith(self.location.origin)
    ) {
        event.respondWith(fetch(event.request));
        return;
    }

    if (event.request.url.includes(`/api/transaction`)) {
        event.respondWith(
            caches.open(RUNTIME_CACHE).then(cache =>
                fetch(event.request)
                    .then(response => {
                        cache.put(event.request, response.clone());
                        return response;
                    })
                    .catch(() => caches.match(event.request))
            )
        );
        return;
    }

    event.respondWith(
        caches.match(event.request).then(cachedResponse => {
            if (cachedResponse) {
                return cachedResponse;
            }

            return caches
                .open(RUNTIME_CACHE)
                .then(cache =>
                    fetch(event.request).then(response =>
                        cache.put(event.request, response.clone()).then(() => response)
                    )
                );
        })
    );
});

```
- The service worker is registered/linked in the front end side (main html ``` index.html ``` ).

``` bash 
 <script>
            if ('serviceWorker' in navigator) {
                window.addEventListener('load', () =>
                    navigator.serviceWorker
                        .register('service-worker.js')
                        .then(reg => console.log('We found your service worker file!', reg))
                );
            }
</script>

```