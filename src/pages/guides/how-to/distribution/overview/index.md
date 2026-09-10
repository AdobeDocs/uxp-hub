---
title: Package and Distribute
description: How to package and distribute a UXP plugin through the Creative Cloud Marketplace, independently, or within an enterprise.
keywords:
  - UXP Plugins
  - Distribution
  - Creative Cloud Marketplace
  - CCX
  - Plugin Publishing
  - Plugin Packaging
  - Distribution Channels
  - Creative Cloud Desktop
contributors:
  - https://github.com/karan0207
---

<Superhero slots="heading, text" variant="centered" textColor="white" background="linear-gradient(135deg, #30186E 0%, #6432C8 100%)"/>

# Package and Distribute

Once your plugin works, package it as a CCX file, test the installer, and choose how to deliver it. The package format is the same for every distribution channel and supported host application.

## Choose a distribution path

<Cards slots="heading, text, links" repeat="3" width="100%"/>

### Creative Cloud Marketplace

For public discovery and free or paid plugins. Adobe review is required; users install through Creative Cloud Desktop.

[Publish to Marketplace](../adobe-marketplace/index.md)

### Independent Distribution

For your website, GitHub, or a third-party store. Adobe review isn't required; you provide the `.ccx` installer.

[Distribute independently](../independent-distribution/index.md)

### Enterprise Distribution

For managed deployment within an organization. Adobe review isn't required; administrators deploy through Admin Console or UPIA.

[Deploy in an enterprise](../enterprise-distribution/index.md)

## Prepare your plugin

1. [Package your plugin](../package/index.md) to build the `.ccx` installer.
2. [Install the plugin](../install/index.md) and test it in the host application.
3. Follow the guide for your chosen distribution channel.

<InlineAlert variant="info" slots="heading, text"/>

Publish through multiple channels

If you publish through Creative Cloud Marketplace and another channel, use a separate plugin ID for the Marketplace package. See [Package a Plugin](../package/index.md#multi-channel-distribution) for details.
