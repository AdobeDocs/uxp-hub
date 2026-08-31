---
title: Independent Distribution
description: Share a UXP plugin directly through a website, repository, third-party store, or another trusted channel.
keywords:
  - UXP Plugins
  - Distribution
  - Independent Distribution
  - Direct Distribution
  - CCX
  - Creative Cloud Marketplace
contributors:
  - https://github.com/karan0207
---

# Distribute independently

Independent distribution lets you provide a `.ccx` installer directly to users without submitting the plugin to Creative Cloud Marketplace.

## Share the installer

After [packaging the plugin](../package/index.md), host the `.ccx` file on a website, GitHub release, third-party store, or another trusted delivery channel.

Users follow the standard [`.ccx` installation flow](../install/index.md#use-a-ccx-installer-file). Creative Cloud Desktop warns that packages outside Marketplace should come from a trusted source.

<InlineAlert variant="info" slots="text"/>

Use a separate plugin ID only when the same plugin is also published through Creative Cloud Marketplace. See [multi-channel distribution](../package/index.md#multi-channel-distribution).

## Choose another channel

For managed internal deployment, use [Enterprise Distribution](../enterprise-distribution/index.md). For public discovery through Adobe, [publish to Marketplace](../adobe-marketplace/index.md).
