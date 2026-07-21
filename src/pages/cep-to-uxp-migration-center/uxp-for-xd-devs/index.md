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
title: UXP in Photoshop vs Other Host Applications
description: The main differences to expect when moving a UXP plugin from one host application to another.
---

# UXP in Photoshop vs Other Host Applications

UXP is available in Photoshop, Premiere Pro, Media Encoder, InDesign, and Adobe XD. If you've already built a UXP plugin for one of these and are bringing it to Photoshop, here are the main differences to expect.

## Manifest JSON

Manifest versions and structure vary by host application and by how recent your existing plugin is. If you're porting a plugin to Photoshop, check the current structure of the [manifest.json](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/manifest-v4/?aio_external=true) file for Photoshop before assuming your existing manifest will work as-is.

## Host application API

Every host application exposes a different DOM and API, since each one has a different set of use cases, objects, and properties. Photoshop in particular is a large, complex application with a correspondingly broad API surface.

The [Photoshop API](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/?aio_external=true) grows with each release, but until the API surface covers every object you need, you can use [batchPlay](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true) to reach objects, properties, and actions not yet exposed directly through UXP.
