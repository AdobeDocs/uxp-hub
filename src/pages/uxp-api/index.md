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

<Superhero slots="heading, text" variant="centered" textColor="white" background="linear-gradient(135deg, #30186E 0%, #6432C8 100%)"/>

# UXP API Reference

The UXP API is the platform layer shared across UXP-enabled host applications: JavaScript, CSS, HTML, and Spectrum UI, plus modules for file system access, networking, storage, and entry points.

Learn the platform layer once, and it carries over everywhere. Each host application then adds its own DOM API on top - for documents, layers, sequences, and other host-specific objects - which is what you'll reach for once you're past the platform basics covered here.

<Cards slots="image, heading, text, links" repeat="4" width="100%" />

![JavaScript](../assets/javascript.svg)

### JavaScript

Core APIs for crypto, storage, networking, and file system access in plugins.

[JavaScript reference](reference-js/index.md)

![CSS](../assets/css.svg)

### CSS

Selectors, pseudo-classes, media queries, and properties for panel layout.

[CSS reference](reference-css/index.md)

![HTML](../assets/html.svg)

### HTML

Elements, attributes, and document structure for building consistent panel interfaces.

[HTML reference](reference-html/index.md)

![Spectrum](../assets/spectrum.svg)

### Spectrum UXP

Spectrum components for building interfaces that match the host application.

[Spectrum reference](reference-spectrum/index.md)

## Host Application DOM APIs

Each host application layers its own DOM API for documents, layers, sequences, and other host-specific objects on top of the platform layer above.

<Cards slots="image, heading, text, links" repeat="4" width="100%" />

![Photoshop](../assets/photoshop.svg)

### Photoshop

Imaging APIs for documents, layers, selections, and Camera Raw.

[Photoshop API](https://developer.adobe.com/photoshop/uxp/)

![InDesign](../assets/indesign.svg)

### InDesign

Layout APIs for documents, pages, stories, styles, and frames.

[InDesign API](https://developer.adobe.com/indesign/uxp/)

![Premiere](../assets/premiere-pro.svg)

### Premiere

Video APIs for projects, sequences, tracks, source media, markers, and exports.

[Premiere API](https://developer.adobe.com/premiere-pro/uxp/)

![Media Encoder](../assets/media-encoder.svg)

### Media Encoder

Encoding APIs for presets, codecs, render queues, output settings, and exports.

[Media Encoder API](https://developer-stage.adobe.com/media-encoder/uxp/)

## Known Issues

Exact support can vary by host application and UXP version. See [Known Issues](known-issues.md) for documented platform-level limitations and workarounds.

## Version Details and Change Log

Track which UXP version ships in each host application, and what's new, changed, or fixed in each release.

<Cards slots="image, heading, text, links" repeat="2" width="100%" />

![Version Details](../assets/version-details.svg)

### Version Details

Which UXP version ships in each host application, plus the ECMAScript and React versions it supports.

[View version details](versions.md)

![Change Log](../assets/changelog.svg)

### Change Log

What's new, changed, deprecated, and fixed in each UXP release across supported hosts.

[View the change log](changelog.md)
