---
title: UXP in Photoshop vs Other Host Applications
description: The main differences to expect when moving a UXP plugin from one host application to another.
keywords:
  - Creative Cloud
  - API Documentation
  - UXP
  - Plugins
  - JavaScript
  - ExtendScript
  - SDK
  - Scripting
contributors:
  - https://github.com/kasivn
---

# UXP in Photoshop vs Other Host Applications

This page covers the differences to consider when adapting a UXP plugin built for Premiere, InDesign, or Media Encoder for Photoshop.

## Manifest JSON

Manifest versions and structure vary by host application and by how recent your existing plugin is. If you're porting a plugin to Photoshop, check the current structure of the [manifest.json](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/manifest-v4/?aio_external=true) file for Photoshop before assuming your existing manifest will work as-is.

## Host application API

Every host application exposes a different DOM and API, since each one has a different set of use cases, objects, and properties. Photoshop in particular is a large, complex application with a correspondingly broad API surface.

The [Photoshop API](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/?aio_external=true) grows with each release. When the Photoshop DOM API does not expose an operation you need, you can use [batchPlay](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true) to access Photoshop objects, properties, and actions. `batchPlay` is specific to Photoshop and is not a cross-host UXP fallback.
