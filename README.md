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