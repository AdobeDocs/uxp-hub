---
keywords:
  - Creative Cloud
  - API Documentation
  - UXP
  - Plugins
  - JavaScript
  - ExtendScript
  - SDK
  - Scripting
title: UXP for Web Developers
description: What's familiar and what's different when you build UXP plugins as a web developer.
---

# UXP for Web Developers

At first glance, developing for UXP in Adobe applications looks familiar to people coming from web development. UXP uses the familiar structure of HTML, CSS, and JavaScript to build what look like web pages, but are actually panels and dialogs.

Knowing HTML, CSS, and JavaScript isn't enough on its own. As a UXP developer, you also need to be familiar with the host application's API. This is where the web and UXP diverge most: there's nothing like an application API for websites, but to do anything useful in a UXP-enabled host application, you need to know the app.

For Photoshop, the API documentation is [here](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/?aio_external=true). For InDesign, it's [here](https://developer.adobe.com/indesign/uxp/dom/api/?aio_external=true). For Premiere Pro, it's [here](https://developer.adobe.com/premiere-pro/uxp/ppro-reference/?aio_external=true).

UXP plugins also have a different directory structure than typical websites. A required [manifest file](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/manifest-v4/?aio_external=true) sits at the top level of the plugin directory, along with a set of icons used when a panel is minimized in the host application. These icons are optional during development, but mandatory for distribution: a plugin without the proper icon structure will fail submission.
