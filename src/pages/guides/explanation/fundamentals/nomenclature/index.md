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
- **Manifest** (`manifest.json`): declares the plugin's identity, the hosts it targets, its permissions, and its entry points.
- **Entry point**: a declared way for the host to invoke your plugin, such as a panel or a command.
- **Panel**: an entry point that shows a persistent, dockable UI inside the host application.
- **Command**: an entry point, shown as a menu item, that runs an action without a persistent panel.
- **Script**: a single JavaScript file that automates the host through its APIs. A script has no manifest and no persistent panel. It is a separate artifact from a plugin.
- **Host**: the Adobe application your plugin runs in, such as Photoshop, InDesign, Premiere, or Media Encoder.

<InlineAlert slots="text" />

More precisely, a UXP _plugin_ is a container of either _panel(s)_, _command(s)_, or both. This mirrors, and extends, how the CEP ecosystem used _extensions_ to contain _panel(s)_.

## A brief history of the terminology

Over the years, Adobe Creative Cloud applications have supported ExtendScript **Scripts**, Flash **Panels**, CEP **Extensions**, and now UXP **Plugins** (either _regular_ or _hybrid_) and UXP **Scripts**. Uniquely, Adobe Express deals with **add-ons** instead.

Many desktop applications also support compiled native plugins, such as effects in Premiere or filters in Photoshop. In these docs, **plugin** refers to a UXP plugin. It contains one or more panels or commands, and it is the successor to the CEP extension. This keeps the vocabulary consistent with the other Adobe applications that have moved, or are moving, to UXP. Scripts are a separate artifact, not a type of plugin.

## Mapping from CEP and ExtendScript

If you've built extensions before, this is roughly how the old vocabulary maps to UXP:

| Legacy (CEP / ExtendScript) | UXP |
| --- | --- |
| CEP extension panel | UXP plugin with a panel entry point |
| ExtendScript (`.jsx`) automation | UXP script using the host APIs |
| `CSInterface` / host bridge | the host APIs you `require` directly |
| Manifest in `CSXS/manifest.xml` | `manifest.json` |

The mapping is conceptual, not line for line. For the practical migration path, see [Migrate from CEP and ExtendScript](../../../../migration-center/index.md).
