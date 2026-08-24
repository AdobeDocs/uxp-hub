---
title: "Unified Extensibility Platform"
description: "Build UXP plugins across UXP-enabled Creative Cloud apps on one shared platform."
keywords:
  - UXP
  - Photoshop
  - InDesign
  - Premiere
  - Media Encoder
contributors:
  - https://github.com/icaraps 
---

<Superhero slots="heading, text" variant="centered" textColor="white" background="rgb(11, 87, 173)"/>

# Unified Extensibility Platform

UXP is the shared platform for building plugins across UXP-enabled Creative Cloud apps. Whether you're writing your first panel or shipping your tenth, this is where you start.

## What you can build

Every UXP plugin is built from one or both of two component types:

- **Commands are actions.** They run from a menu item, execute one task, and finish. There's no persistent UI beyond an optional dialog for input or confirmation.
- **Panels are workspaces.** They stay docked alongside the host's own panels, persistent and interactive throughout your session.

What you reach for depends on the host: layers and documents in Photoshop, pages and stories in InDesign, or sequences and exports in Premiere. The plugin model is the same everywhere, and so is the panel/command choice. See [Panels and Commands](guides/explanation/concepts/panels-and-commands/index.md) for the full picture, including modal dialogs and combining both in one plugin.

## Start building

Every UXP plugin follows the same path, regardless of host: scaffold it, load it into your host application, build the UI, and ship it. [Guides](guides/index.md) walks through all of it: getting set up, core concepts and tutorials, and how to package and distribute what you build.
