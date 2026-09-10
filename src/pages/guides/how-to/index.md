---
title: How-to Guides
description: Task-oriented guides for adding commands, lifecycle hooks, panels, dialogs, and plugin communication to UXP plugins.
keywords:
  - UXP
  - How-to guides
  - Hybrid plugins
  - Distribution
  - Recipes
  - Migration guides
contributors:
  - https://github.com/karan0207
---

<Superhero slots="heading, text" variant="centered" textColor="white" background="linear-gradient(135deg, #30186E 0%, #6432C8 100%)"/>

# How-to Guides

Add commands, lifecycle hooks, panels, modal dialogs, and communication between installed plugins.

These guides assume your plugin is scaffolded and loading. Start with [Build Your First Plugin](../tutorials/build-your-first-plugin/index.md) if it is not, or review [Plugin Concepts](../explanation/index.md) for manifests, entrypoints, panels, and commands.

## Add a feature

<DiscoverBlock slots="link, text" width="300px"/>

[Add Commands](add-commands/index.md)

Create menu actions that run your plugin code.

<DiscoverBlock slots="link, text" width="300px"/>

[Add Lifecycle Hooks](add-lifecycle-hooks/index.md)

Run setup and teardown logic when plugins and panels change state.

<DiscoverBlock slots="link, text" width="300px"/>

[Add Multiple Panels](add-panels/index.md)

Create multiple panel entrypoints and open them programmatically.

<DiscoverBlock slots="link, text" width="300px"/>

[Add Modal Dialogs](add-modal-dialogs/index.md)

Create modal dialogs as a user interface for commands, or as additional UI for panels.

<DiscoverBlock slots="link, text" width="300px"/>

[Communicate Between Plugins](inter-plugin-comm/index.md)

Open another installed plugin's panel or invoke one of its commands.

## Related

Other how-to sections, each in the sidebar:

- **[Package & Distribute](distribution/overview/index.md):** package a `.ccx` and publish through Adobe Marketplace, independently, or within an enterprise.
- **Recipes:** copy-paste-ready snippets for filesystem access, networking, clipboard, CSS, debugging, and more.
- **[Hybrid Plugins](hybrid-plugins/index.md):** extend a plugin with high-performance native C++ libraries. **Premiere and Photoshop only.**
- **Migrate to UXP:** move an existing CEP or ExtendScript extension to a UXP plugin.

Haven't set up your tools yet? Start with [Set Up Developer Tools](developer-tools/index.md).
