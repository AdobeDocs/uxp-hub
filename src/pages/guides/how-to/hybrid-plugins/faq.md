---
title: Hybrid Plugins FAQ
description: Resolve common Hybrid plugin questions about signing, architectures, compatibility, loading, and native dependencies.
keywords:
  - UXP Hybrid
  - Hybrid Plugins FAQ
  - C++
  - uxpaddon
  - code signing
  - architectures
contributors:
  - https://github.com/karan0207
---

# Hybrid plugin FAQ

Use these answers when preparing native binaries, testing supported architectures, or diagnosing an addon that does not load.

<InlineAlert variant="info" slots="text" />

Hybrid plugins are supported in **Premiere** and **Photoshop**, not currently in Media Encoder or InDesign.

<AccordionItem slots="heading, text" repeat="7"/>

### Do I need to code sign the entire plugin bundle?

No. Only the macOS `.uxpaddon` executables need to be signed and notarized with a valid Apple Developer ID certificate. The rest of the plugin bundle (JavaScript, HTML, CSS, manifest) does not require code signing.

### Do I need an Apple Developer ID?

Yes. macOS requires a Developer ID-signed certificate for notarized executables. See [Apple's code signing guide](https://support.apple.com/guide/security/app-code-signing-process-sec3ad8e6e53/web) for details.

### How do I prepare and test binaries for all architectures?

Build and test binaries for macOS arm64, macOS x64, and Windows x64. See [Apple's universal binary guide](https://developer.apple.com/documentation/apple-silicon/building-a-universal-macos-binary) when combining macOS architectures.

Use native hardware or a compatible virtual machine for platforms you do not have locally. A package with only some architectures can support controlled development or independent distribution, but it will not load where a matching binary is absent. The **Creative Cloud Marketplace requires all three architectures**.

### Are Hybrid plugins forward-compatible?

An addon built with an older supported SDK remains compatible with newer supported Premiere and Photoshop releases. Adopting a newer SDK requires rebuilding and republishing the native binaries. Check each host's release notes before upgrading.

### Why can't I see the plugin in Premiere after loading it?

After loading through UDT, clear and reselect the plugin under **Window** > **UXP Plugins** if it does not appear immediately. Then check the UDT logs for manifest or addon-loading errors.

### The macOS binaries trigger security warnings. What should I do?

The SDK's `template-plugin` binaries are not signed. For local testing on macOS, allow them under **System Settings** > **Privacy & Security**. Sign and notarize every production `.uxpaddon` with a valid Apple Developer ID.

### Why do I get "Failed to load Addon" with "The specified module could not be found" on Windows?

This usually means your `.uxpaddon` was built in Debug mode, which depends on Visual Studio debug runtimes that are not present on end-user systems. It may work on development machines but fail on clean Windows installs. Rebuild the addon in Release mode (and ensure correct project settings, such as `.uxpaddon` output and no debug dependencies) and redistribute it.

## Continue building

See [Build a Hybrid Plugin](build.md) for the complete native build, manifest, debugging, and packaging workflow.
