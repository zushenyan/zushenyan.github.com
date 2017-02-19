---
title: Introduction to Progressive Web App (PWA)
date: 2017-02-13 22:37:45
tags: [frontend, pwa, mobile, javascript]
---

# What is that?
Buzz word time! Progressive Web App (will be written as PWA in the following post), promoted by Google, basically means delivering user an app-like web app. It concludes 3 important features:

- Responsive - available on many devices: desktop, phone and tablet.
- Reliable - accessible even if the network is down.
- Fresh - always up-to-date, updating without getting through app store.
- Safe - can only be accessed via HTTPS.
- Discoverable - unlike native app, since it's an web app, content should be discoverable by the search engines.
- Engaging - PWA can be access like an native app. You can access them by adding them to home screen, and provide them an seamless full screen experience.

but that sounds very...unclear. We want to know how it was made right. Here is the *heart of Progressive Web App*:

- [Service Worker](https://w3c.github.io/ServiceWorker/) - the first pillar of PWA. It's an API that hijacks networking request, and responds browser with additional messages.
- [Cache Storage](https://developer.mozilla.org/en-US/docs/Web/API/CacheStorage) - the second pillar of PWA. A member of Web Storage. An API developer often use with Service Worker. It caches request as key and responds with cached value if corresponding key is found.
- [Web App Manifest](https://w3c.github.io/manifest/) - a JSON-based manifest file provides ability to set things like app icon name or display mode and many other things.

# Platform support
[service worker API on MDN](https://developer.mozilla.org/en-US/docs/Web/API/serviceworker)

In the current status, service worker is not available on iOS, which says your app still can't benefit from PWA, but the good news is even if the working environment hasn't support yet, your app will fallback to the traditional caching strategy. Your app still works without modifying codes!

# PWA vs Native
PWA is an improvement to the web and user experience, making it more like an native app, but that doesn't mean it's going to replace native app or against native app on par.

PWA is just a collection of features and it's not a platform like native. A fair fight should be "browser vs native".

If you take a look at [what web can do](https://whatwebcando.today/), you might think "cool I can finally develop something like native does on mobile!" However the current compatibility on iOS still has long way to go, if you take a look on those API closely, you will find out most of them only available on Chrome on Android.

# How does it work?
Please check [pwa-test](https://github.com/zushenyan/pwa-test) written by me. It's a simplified version of [Your Frist Progressive Web App](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0).

I don't want to write every step down here, because, first, I am lazy, and more important, I think [Your Frist Progressive Web App](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0) has done a great job on tutoring. I believe it already provides all the details you need.

However there are still some pitfalls when developing with service worker, in order to save your time, [here](https://gist.github.com/Rich-Harris/fd6c3c73e6e707e312d7c5d7d0f3b2f9) you go.

# Higher level API
After read all the tutorials, you found you have to write many codes to achieve PWA, but... you are a lazy programmer like me, and wondering "where's the higher level API candy?" Well, people heard you. Here comes some candies!

- [sw-toolbox](https://github.com/GoogleChrome/sw-toolbox) - a collection of service worker tools to help developing offline web app.
- [application-shell](https://github.com/GoogleChrome/application-shell) - a helper for integrating service worker with building chain like gulp.
- [webpack-offline-plugin](https://github.com/NekR/offline-plugin) - a webpack plugin for building offline web app.

# References
- [What is a progressive web app](http://blog.ionic.io/what-is-a-progressive-web-app/)
- [A Beginner’s Guide To Progressive Web Apps](https://www.smashingmagazine.com/2016/08/a-beginners-guide-to-progressive-web-apps/)
- [Your Frist Progressive Web App](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0)
- [Push notifications](https://codelabs.developers.google.com/codelabs/push-notifications/index.html?index=..%2F..%2Findex#6)
- [Why “Progressive Web Apps vs. native” is the wrong question to ask](https://www.google.com.tw/search?safe=off&q=pwa+vs+native&oq=pwa+vs+&gs_l=serp.3.1.0l7j0i10k1j0l2.44121.47105.0.48621.15.11.4.0.0.0.144.882.10j1.11.0....0...1c.1.64.serp..0.13.779.0..0i22i30k1.fl5zTNkR8kI)
