---
title: Outside Marketplace
description: Deliver a UXP plugin directly to users or deploy it across an organization without a Marketplace listing.
keywords:
  - UXP Plugins
  - Distribution
  - Independent Distribution
  - Enterprise Distribution
  - CCX
contributors:
  - https://github.com/karan0207
---

<Superhero slots="heading, text" variant="centered" textColor="white" background="linear-gradient(135deg, #30186E 0%, #6432C8 100%)"/>

# Outside Marketplace

Deliver a CCX installer through your own channel or deploy it across an organization. Neither path requires a Creative Cloud Marketplace listing or Adobe review.

## Choose how to deliver

<Cards slots="heading, text, links" repeat="2" width="100%"/>

### Independent Distribution

Choose direct delivery through your website, GitHub, a third-party store, or another trusted channel. You manage availability, updates, licensing, and support.

[Distribute independently](../independent-distribution/index.md)

### Enterprise Deployment

Choose managed internal deployment through Adobe Admin Console, deployment packages, or UPIA. Administrators control installation and availability.

[Deploy in an enterprise](../enterprise-distribution/index.md)

## Prepare the package

1. Set a stable plugin ID for non-Marketplace distribution.
2. [Package the plugin](../package/index.md) as a `.ccx` installer.
3. [Test the installer](../install/index.md) in every supported host and platform.
4. Follow the independent or enterprise guide for delivery.

<InlineAlert variant="info" slots="heading, text"/>

Publishing through multiple channels

If the plugin is also available through Creative Cloud Marketplace, use a separate ID for the Marketplace package. See [multi-channel distribution](../package/index.md#multi-channel-distribution) for details.