---
title: UXP
description: "Build UXP plugins across UXP-enabled Creative Cloud apps on one shared platform."
keywords:
  - UXP
  - Photoshop
  - InDesign
  - Premiere
  - Media Encoder
contributors:
  - https://github.com/karan0207
---

<Superhero slots="heading, text" variant="centered" textColor="white" background="rgb(11, 87, 173)"/>

# Unified Extensibility Platform

UXP is the shared platform for building plugins across UXP-enabled Creative Cloud apps. Whether you're writing your first panel or shipping your tenth, this is where you start.

## What you can build

Every UXP plugin is built from one or both of two component types:

- **Commands are actions:** They run from a menu item, execute one task, and finish. There's no persistent UI beyond an optional dialog for input or confirmation.
- **Panels are workspaces:** They stay docked alongside the host's own panels, persistent and interactive throughout your session.

What you reach for depends on the host: layers and documents in Photoshop, pages and stories in InDesign, or sequences and exports in Premiere. The plugin model is the same everywhere, and so is the panel/command choice. See [Panels and Commands](guides/explanation/concepts/panels-and-commands/index.md) for the full picture, including modal dialogs and combining both in one plugin.

## Start building

Every UXP plugin follows the same path, regardless of host: scaffold it, load it into your host application, build the UI, and ship it. [Guides](guides/index.md) walks through all of it: getting set up, core concepts and tutorials, and how to package and distribute what you build.

## Choose a host

Pick the application you already spend your time in for its API reference and product-specific docs. UXP itself works the same way underneath each of them.

<Cards slots="image, heading, text, links" repeat="4" width="100%" />

![Photoshop](assets/photoshop.svg)

### Photoshop

Automate imaging work: documents, layers, selections, and actions.

[Explore Photoshop](https://developer.adobe.com/photoshop/uxp/)

![InDesign](assets/indesign.svg)

### InDesign

Build tools for layout and publishing: pages, stories, styles, and frames.

[Explore InDesign](https://developer.adobe.com/indesign/uxp/)

![Premiere](assets/premiere-pro.svg)

### Premiere

Build tools for editorial work: projects, sequences, tracks, markers, and exports.

[Explore Premiere](https://developer.adobe.com/premiere-pro/uxp/)

![Media Encoder](assets/media-encoder.svg)

### Media Encoder

Automate delivery work: presets, codecs, render queues, and batch exports.

[Explore Media Encoder](https://developer-stage.adobe.com/media-encoder/uxp/)

## How UXP works

A UXP plugin runs inside the host application on a single JavaScript engine. That engine reaches both the shared UXP APIs and the host's own API directly, with none of the ExtendScript bridging or per-plugin Chromium instances that CEP relied on. Your calls are async, so they don't freeze the host while they run.

![Conceptual UXP architecture showing the plugin loader, rendering and layout engines, JavaScript engine, common APIs, and host controls and APIs.](assets/uxp-architecture.svg)

Your interface is still HTML and CSS, but UXP isn't a browser. Its layout engine maps what you write to the host's native controls, so panels match the app and render without a heavy embedded browser. Support is deliberate: an element, CSS property, or web API works only when UXP implements it. Check the [shared UXP API](uxp-api/index.md) for what's available, then use your host's API reference to work with documents, projects, and timelines.

## More resources

Once your plugin runs, the next questions are usually about testing, packaging, and getting it in front of people. Start here:

- **Developer journey:** the full build-and-ship path, from setup to shipping, is covered in [Guides](guides/index.md).
- **UXP API reference:** check the [UXP APIs](uxp-api/index.md) section for what's available on the shared platform.
- **[Share and distribute your plugin](guides/how-to/distribution/overview/index.md):** package it, publish through Adobe Marketplace, or distribute independently and within an enterprise.

## Join the community

Join Creative Cloud developers building plugins and integrations of their own. Ask a question, compare notes, or just see what other people are building:

- Ask questions and share knowledge in the [Creative Cloud Developer Forums](https://forums.creativeclouddeveloper.com/).
- Subscribe to the [Creative Cloud Developer Newsletter](https://www.adobe.com/subscription/ccdevnewsletter.html).
