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

<Superhero slots="heading, text" variant="centered" textColor="white" background="linear-gradient(135deg, #30186E 0%, #6432C8 100%)"/>

# CEP to UXP Migration Center

Planning to move a CEP extension to UXP? Start here. Every host application has its own migration guide, since the specific APIs, manifest format, and packaging steps differ, but the approach below applies no matter which application you're building for.

## How to approach it

There's no single fixed recipe, since every CEP plugin is different, but here's a sample approach that works for most:

1. **Inventory what your CEP extension actually depends on.** Go through your `manifest.xml` and code, and sort what you find into categories: ExtendScript/`evalScript` calls, native CEP functions (`window.cep.fs`, `window.cep.process`, `window.cep.encoding`, `window.cep.util`), CEP JavaScript libraries (`CSInterface`, Vulcan), and any Node.js modules you're relying on through CEP's unrestricted access. This inventory becomes your migration checklist, separate from the code that implements it.
2. **Check what's already built in.** Some features that once required a CEP extension are now native in the latest version of the host application. Confirm before you rebuild anything; you may be able to drop a feature entirely.
3. **Map each remaining item in your checklist to its UXP equivalent.** A few of the most common ones:
   - `window.cep.fs` → UXP's `storage` module, which requires user consent (a file picker) for most file access instead of CEP's unrestricted access.
   - `window.cep.process` → UXP has no comprehensive process API; the closest options are `shell.openPath()` and `shell.openExternal()` for launching files and URIs, gated by the `launchProcess` permission in the manifest.
   - ExtendScript/`evalScript` → the host application's UXP DOM API directly, or [`batchPlay`](https://developer.adobe.com/photoshop/uxp/2022/ps_reference/media/advanced/batchplay/) for anything the DOM API doesn't cover yet.
   - `CSInterface` → direct calls to host UXP APIs, plus UXP's own lifecycle events (`uxpcreateplugin`, `uxpshowpanel`, and similar) for things CSInterface used events for.
   - Vulcan (cross-plugin messaging) → `invokeCommand` and `showPanel` (UXP 6.0.2+, manifest v5), currently limited to plugins within the same host application.

   See [Migrating Native CEP Functions](uxp-for-cep-devs/technical-migration-guide/index.md#migrating-native-cep-functions), [Migrating CEP JavaScript Libraries](uxp-for-cep-devs/technical-migration-guide/index.md#migrating-cep-javascript-libraries), and [Migrating ExtendScript/EvalScript to the Photoshop DOM API](uxp-for-cep-devs/technical-migration-guide/index.md#migrating-extendscriptevalscript-to-the-photoshop-dom-api) in the Technical Migration Guide for the full API-by-API breakdown, including limitations and permission requirements for each.
4. **Rebuild feature by feature.** Start with `manifest.json` and your `entrypoints.setup()` handlers, then work through the UI and each mapped feature using the manifest, entry point, and packaging guidance in the guides below.
5. **If a feature still isn't covered**, for example something that depends on native code, an external process, or a performance-intensive operation, look into building a Hybrid Plugin instead of waiting for a UXP API to catch up. A Hybrid Plugin combines a UXP plugin with C++ native libraries, so you can keep that logic and call it from UXP. See [Related Resources](#related-resources) below for the Hybrid Plugin guides.

## Where to Start

<Cards slots="image, heading, text, links" repeat="3" width="100%" />

![CEP to UXP Technical Migration Guide](../assets/migration.svg)

### CEP to UXP Technical Migration Guide

What's different between CEP and UXP, and how to migrate each part of your extension, API by API.

[Read the technical migration guide](uxp-for-cep-devs/technical-migration-guide/index.md)

![UXP for ExtendScript Developers](../assets/extendscript.svg)

### UXP for ExtendScript Developers

What changes when you move from ExtendScript and the ExtendScript Toolkit to UXP.

[Read the ExtendScript guide](uxp-for-extendscript-devs/index.md)

![UXP in Photoshop vs Other Host Applications](../assets/cross-host.svg)

### UXP in Photoshop vs Other Host Applications

The main differences to expect when moving a UXP plugin from one host application to another.

[Read the cross-host differences](cross-host-differences/index.md)

## What changes at a glance

* **No embedded Chromium.** CEP ran each extension in its own full Chromium instance. UXP plugins run in a single shared, sandboxed JavaScript engine, with no Chromium Embedded Framework bridge and no `CSInterface`.
* **One JavaScript engine, not two.** CEP split logic between a Chromium panel and ExtendScript host code, connected through `evalScript` calls. UXP plugins call host DOM APIs directly from the same JavaScript context.
* **A new manifest.** `manifest.xml` becomes `manifest.json`. Panel entry points, permissions, and plugin IDs are declared differently; see each host's migration guide for the exact mapping.
* **Sandboxed by default.** UXP plugins declare the file system, network, and process permissions they need in the manifest, instead of relying on unrestricted Node.js access. If your CEP extension shelled out to Node.js, plan for a manifest permission review; see the Knowledge Base for a documented pattern for restructuring around this constraint.
* **Native UI controls.** UXP plugins can use Spectrum UI components that match the host application's own interface, instead of hand-styling HTML to approximate it.

## Related Resources

<Cards slots="image, heading, text, links" repeat="2" width="100%" />

![Premiere](../assets/premiere-pro.svg)

### Premiere: Hybrid Plugins

Extend a UXP plugin with high-performance C++ native libraries in Premiere.

[Read the Premiere hybrid plugin guide](https://developer.adobe.com/premiere-pro/uxp/plugins/hybrid-plugins/?aio_external=true)

![Photoshop](../assets/photoshop.svg)

### Photoshop: Hybrid Plugin

Combine a UXP plugin with C++ native libraries in Photoshop.

[Read the Photoshop hybrid plugin guide](https://developer.adobe.com/photoshop/uxp/2022/guides/hybrid-plugins/?aio_external=true)
