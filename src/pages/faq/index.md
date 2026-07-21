---
title: Frequently Asked Questions
description: Frequently asked questions about UXP plugin development.
keywords:
  - FAQ
  - Frequently Asked Questions
  - UXP
  - UXP Developer Tool
  - UXP Developer Tool FAQ
---

# Frequently Asked Questions

This section contains frequently asked questions about UXP. For Hybrid Plugin specific questions, see the [Hybrid Plugin guides](../cep-to-uxp-migration-center/index.md#related-resources) in the CEP to UXP Migration Center.

## Questions

### 🛠️ Development Environment & Tooling

- [How can I enable Developer Mode?](#how-can-i-enable-developer-mode)
- [In UDT, I get the error "Plugin Load Failed, Host Application specified is not available. Make sure the host application is started."](#in-udt-i-get-the-error-plugin-load-failed-host-application-specified-is-not-available-make-sure-the-host-application-is-started)

### 🎨 User Interfaces

- [How can I use Spectrum Web Components in my plugin?](#how-can-i-use-spectrum-web-components-in-my-plugin)
- [Can I use React Spectrum instead of Spectrum Web Components?](#can-i-use-react-spectrum-instead-of-spectrum-web-components)

### 📦 Installers and Packages

- [I am unable to install a plugin.](#i-am-unable-to-install-a-plugin)
- [My `.ccx` package is being rejected when submitted to the Creative Cloud Marketplace. What could be wrong?](#my-ccx-package-is-being-rejected-when-submitted-to-the-creative-cloud-marketplace-what-could-be-wrong)
- [Should I use an Array or an Object for the `host` property in the `manifest.json`?](#should-i-use-an-array-or-an-object-for-the-host-property-in-the-manifestjson)

### 🇪🇺 EU Compliance & DSA Requirements

- [Why is my plugin not visible in the EU region?](#why-is-my-plugin-not-visible-in-the-eu-region)
- [How can I update my trader details in the publisher profile after submission?](#how-can-i-update-my-trader-details-in-the-publisher-profile-after-submission)
- [What happens if an EU user has a deep link to my plugin and I am not compliant with the European Union Digital Services Act (DSA) trader requirements?](#what-happens-if-an-eu-user-has-a-deep-link-to-my-plugin-and-i-am-not-compliant-with-the-european-union-digital-services-act-dsa-trader-requirements)
- [Can an EU user still use my plugin if they have already installed it, but I am not compliant with the DSA trader requirements?](#can-an-eu-user-still-use-my-plugin-if-they-have-already-installed-it-but-i-am-not-compliant-with-the-dsa-trader-requirements)

## Answers

#### How can I enable Developer Mode?

You need to enable Developer Mode in both the UXP Developer Tool and your host application. Check your host application's own developer documentation for how to enable Developer Mode there.

#### In UDT, I get the error "Plugin Load Failed, Host Application specified is not available. Make sure the host application is started."

Make sure the host application specified in your plugin's manifest is running, and that Developer Mode is enabled in that host application.

#### How can I use Spectrum Web Components in my plugin?

You can use Spectrum Web Components in your plugin by following the instructions in the Spectrum Web Components reference.

#### Can I use React Spectrum instead of Spectrum Web Components?

Given that UXP does not support the entire set of HTML elements and CSS properties, [React Spectrum](https://react-spectrum.adobe.com/react-spectrum/index.html) components may not work as expected. For this reason, we recommend using [React wrappers for Spectrum Web Components](https://opensource.adobe.com/spectrum-web-components/using-swc-react/) instead. Make sure you have enabled the `enableSWCSupport` feature flag in your `manifest.json` file and installed the right version of the components.

#### I am unable to install a plugin.

Refer to the troubleshooting section of [Packaging Your Plugin](https://developer.adobe.com/photoshop/uxp/2022/guides/distribution/packaging-your-plugin/?aio_external=true) for common installation issues.

#### My `.ccx` package is being rejected when submitted to the Creative Cloud Marketplace. What could be wrong?

Check whether the `host` property in your `manifest.json` is an object, not an array. If it's an array, validation will fail in production. See [Manifest v5](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/manifest-v5/?aio_external=true) for the correct `host` property structure.

#### Should I use an Array or an Object for the `host` property in the `manifest.json`?

The `host` property must be a single `HostDefinition` object for production. Arrays are only allowed during development for convenience. See [Manifest v5](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/manifest-v5/?aio_external=true) and [Packaging Your Plugin](https://developer.adobe.com/photoshop/uxp/2022/guides/distribution/packaging-your-plugin/?aio_external=true) for details.

#### Why is my plugin not visible in the EU region?

This could be due to incomplete or outdated trader information in your publisher profile. Make sure all required details are updated and accurate.

#### How can I update my trader details in the publisher profile after submission?

To update only your trader details in the publisher profile after submission, contact the developer distribution team through your host application's own developer support channel. Change requests for other fields in the publisher profile are not processed at this time.

#### What happens if an EU user has a deep link to my plugin and I am not compliant with the European Union Digital Services Act (DSA) trader requirements?

An EU user with a deep link to your plugin will not be able to install it. They will see a banner with a message indicating the compliance issue.

#### Can an EU user still use my plugin if they have already installed it, but I am not compliant with the DSA trader requirements?

Yes. If an EU user has already installed your plugin, they can still use it even if you are not compliant with the DSA trader requirements. However, they will see a banner with a message indicating the compliance issue.
