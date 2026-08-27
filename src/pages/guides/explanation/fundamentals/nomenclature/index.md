---
title: Common Plugin Terms
description: The vocabulary used across the UXP docs: plugin, panel, command, manifest, entry point, script, and how the terminology evolved from ExtendScript and CEP.
keywords:
  - Extensibility
  - Nomenclature
  - Plugins
  - Panels
  - Extensions
  - UXP
  - CEP
  - glossary
contributors:
  - https://github.com/karan0207
---

# Common Plugin Terms

These docs use a small, consistent vocabulary. This page defines the core terms so the rest of the guides read clearly.

## Core terms

- **Plugin**: the unit you build and load. It bundles a manifest, your code, and assets.
- **Manifest** (`manifest.json`): declares the plugin's identity, the host it targets, and its entry points.
- **Entry point**: a declared way into your plugin, such as a panel or a command.
- **Panel**: an entry point that renders UI inside the host application.
- **Command**: an entry point that runs an action without a persistent panel.
- **Script**: JavaScript that drives the host through its APIs, with or without UI.
- **Host**: the Adobe application your plugin runs in, such as Premiere, Media Encoder, Photoshop, or InDesign.

<InlineAlert slots="text" />

More precisely, a _plugin_ is a container of either _panel(s)_, _command(s)_, or both. This mirrors, and extends, how the CEP ecosystem used _extensions_ to contain _panel(s)_.

## A brief history of the terminology

Over the years, Adobe Creative Cloud applications have supported ExtendScript **Scripts**, Flash **Panels**, CEP **Extensions**, and now UXP **Plugins** (either _regular_ or _hybrid_) and UXP **Scripts**. Uniquely, Adobe Express deals with **add-ons** instead.

Most desktop applications also support a different kind of _compiled_ plugin, often spelled **Plug-ins**, for example, Effects in Premiere or Filters in Photoshop. While that continues to be the case, these docs use **plugins** as a catch-all term for _panels_, _extensions_, and, eventually, _scripts_, to stay consistent with the other Adobe Creative Cloud applications that have migrated, or are migrating, to the UXP standard.

## Mapping from CEP and ExtendScript

If you've built extensions before, this is roughly how the old vocabulary maps to UXP:

| Legacy (CEP / ExtendScript) | UXP |
| --- | --- |
| CEP extension panel | UXP plugin with a panel entry point |
| ExtendScript (`.jsx`) automation | UXP script using the host APIs |
| `CSInterface` / host bridge | the host APIs you `require` directly |
| Manifest in `CSXS/manifest.xml` | `manifest.json` |

The mapping is conceptual, not line for line. For the practical migration path, see [Migrate from CEP and ExtendScript](../../../how-to/migration-guides/index.md).
