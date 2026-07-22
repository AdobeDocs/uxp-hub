---
title: "CEP to UXP Migration Center"
description: "Central navigation for moving a CEP extension to UXP, with a guide for every starting point."
keywords:
  - CEP
  - CEP to UXP
  - migration
  - CSInterface
  - UXP
  - ExtendScript
  - Hybrid Plugin
---

# CEP to UXP Migration Center

Planning to move a CEP extension to UXP? Start here. Every host application has its own migration guide, since the specific APIs, manifest format, and packaging steps differ, but the approach below applies no matter which application you're building for.

## How to approach it

There's no single fixed recipe, since every CEP plugin is different, but here's a sample approach that works for most:

1. **Understand what your current CEP plugin actually does.** Write down its features and the ExtendScript or host APIs each one depends on, separately from the code that implements them. This becomes your checklist for the rebuild.
2. **Check what's already built in.** Some features that once required a CEP extension are now native in the latest version of the host application. Confirm before you rebuild anything; you may be able to drop a feature entirely.
3. **Map each remaining feature to the UXP and host APIs available.** Use the UXP API reference for the platform layer shared across host applications, and the host application's own API reference for anything specific to that app.
4. **Rebuild feature by feature**, using the manifest, entry point, and packaging guidance in the guides below.
5. **If a feature still isn't covered**, for example something that depends on native code, an external process, or a performance-intensive operation, look into building a Hybrid Plugin instead of waiting for a UXP API to catch up. A Hybrid Plugin combines a UXP plugin with C++ native libraries, so you can keep that logic and call it from UXP. See [Related Resources](#related-resources) below for the Hybrid Plugin guides.

## Guides

<DiscoverBlock slots="link, text"/>

[UXP for CEP Developers](uxp-for-cep-devs/index.md)

What's different between CEP and UXP, and how to rebuild with UXP.

<DiscoverBlock slots="link, text"/>

[UXP for ExtendScript Developers](uxp-for-extendscript-devs/index.md)

What changes when you move from ExtendScript and the ExtendScript Toolkit to UXP.

<DiscoverBlock slots="link, text"/>

[UXP for Web Developers](uxp-for-web-devs/index.md)

What's familiar and what's different when you build UXP plugins as a web developer.

<DiscoverBlock slots="link, text"/>

[UXP for Newbies](uxp-for-newbies/index.md)

New to JavaScript, HTML, and CSS entirely? Start here.

<DiscoverBlock slots="link, text"/>

[UXP in Photoshop vs Other Host Applications](uxp-for-xd-devs/index.md)

The main differences to expect when moving a UXP plugin from one host application to another.

## What changes at a glance

* **No embedded Chromium.** CEP ran each extension in its own full Chromium instance. UXP plugins run in a single shared, sandboxed JavaScript engine, with no Chromium Embedded Framework bridge and no `CSInterface`.
* **One JavaScript engine, not two.** CEP split logic between a Chromium panel and ExtendScript host code, connected through `evalScript` calls. UXP plugins call host DOM APIs directly from the same JavaScript context.
* **A new manifest.** `manifest.xml` becomes `manifest.json`. Panel entry points, permissions, and plugin IDs are declared differently; see each host's migration guide for the exact mapping.
* **Sandboxed by default.** UXP plugins declare the file system, network, and process permissions they need in the manifest, instead of relying on unrestricted Node.js access. If your CEP extension shelled out to Node.js, plan for a manifest permission review; see the Knowledge Base for a documented pattern for restructuring around this constraint.
* **Native UI controls.** UXP plugins can use Spectrum UI components that match the host application's own interface, instead of hand-styling HTML to approximate it.

## Related Resources

<DiscoverBlock slots="link, text"/>

[Premiere: Hybrid Plugins](https://developer.adobe.com/premiere-pro/uxp/plugins/hybrid-plugins/?aio_external=true)

Extend a UXP plugin with high-performance C++ native libraries in Premiere.

<DiscoverBlock slots="link, text"/>

[Photoshop: Hybrid Plugin](https://developer.adobe.com/photoshop/uxp/2022/guides/hybrid-plugins/?aio_external=true)

Combine a UXP plugin with C++ native libraries in Photoshop.
