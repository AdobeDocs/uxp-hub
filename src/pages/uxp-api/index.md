---
title: UXP API Reference
description: "Technical documentation for the UXP platform layer shared by every host app: JavaScript, CSS, HTML, and Spectrum UI, plus known issues and changelog."
keywords:
  - UXP API
  - JavaScript reference
  - CSS reference
  - HTML reference
  - Spectrum UI
  - UXP changelog
---

# UXP API Reference

The UXP API is the platform layer shared across UXP-enabled host applications: JavaScript, CSS, HTML, and Spectrum UI, plus modules for file system access, networking, storage, and entry points. Exact support can vary by host application and UXP version — see [Known Issues](known-issues.md) and [Version Details](versions.md) for specifics.

Learn the platform layer once, and it carries over everywhere. Each host application then adds its own DOM API on top — for documents, layers, sequences, and other host-specific objects — which is what you'll reach for once you're past the platform basics covered here.

<DiscoverBlock slots="link, text"/>

[JavaScript Reference](reference-js/index.md)

Global members and `require`-able modules available in every UXP plugin: crypto, storage, data transfers, HTML DOM and elements, and the `fs`, `os`, and `uxp` modules.

<DiscoverBlock slots="link, text"/>

[CSS Reference](reference-css/index.md)

Supported selectors, pseudo-classes, pseudo-elements, media queries, and style properties.

<DiscoverBlock slots="link, text"/>

[HTML Reference](reference-html/index.md)

Supported HTML elements, attributes, and document hierarchy.

<DiscoverBlock slots="link, text"/>

[Spectrum UXP Reference](reference-spectrum/index.md)

Spectrum UXP Widgets and Spectrum Web Components for building UI that matches the host application.

<InlineAlert variant="info" slots="text"/>

Looking for a specific host application's own DOM API (documents, layers, sequences)? Those live under [UXP APIs > Host Application DOM APIs](#host-application-dom-apis) below, not here.

## Host Application DOM APIs

Each host application layers its own DOM API for documents, layers, sequences, and other host-specific objects on top of the platform layer above.

<DiscoverBlock slots="link, text"/>

[Photoshop API](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/?aio_external=true)

DOM API for images, layers, documents, and Camera Raw workflows in Photoshop.

<DiscoverBlock slots="link, text"/>

[Premiere Pro API](https://developer.adobe.com/premiere-pro/uxp/ppro-reference/?aio_external=true)

DOM API for projects, sequences, tracks, and media in Premiere Pro.

<DiscoverBlock slots="link, text"/>

[InDesign API](https://developer.adobe.com/indesign/uxp/dom/api/?aio_external=true)

DOM API for documents, pages, and layout objects in InDesign.

## More

<DiscoverBlock slots="link, text"/>

[Known Issues](known-issues.md)

Current platform-level limitations and workarounds.

<DiscoverBlock slots="link, text"/>

[Version Details](versions.md)

Which UXP version ships in which host application, and the ECMAScript and React versions UXP currently supports.

<DiscoverBlock slots="link, text"/>

[Change Log](changelog.md)

What's new, changed, deprecated, and fixed in each UXP release.
