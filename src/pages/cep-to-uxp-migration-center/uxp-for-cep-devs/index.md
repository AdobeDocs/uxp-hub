---
keywords:
  - Creative Cloud
  - API Documentation
  - UXP
  - Plugins
  - JavaScript
  - ExtendScript
  - SDK
  - Scripting
title: UXP for CEP Developers
description: How UXP compares to the Common Extensibility Platform (CEP), and what changes when you build with UXP instead.
---

# UXP for CEP Developers

## How CEP and UXP differ

The Common Extensibility Platform (CEP) has long been the way to build plugins that need more than the simplest of user interfaces. UXP takes a different architectural approach:

* CEP uses a full version of Chromium as a web host, which is resource intensive, especially since each CEP plugin runs in its own instance of Chromium. UXP plugins run in a single shared, lightweight engine.
* CEP doesn't talk directly to the host application. Host scripts are written in ExtendScript and passed to the app through EvalScript calls, so two different JavaScript engines are running at once. A plugin's code ends up artificially split between ExtendScript and JavaScript, and passing anything more than simple parameters between the two layers is awkward and inefficient. UXP plugins call host APIs directly from the same JavaScript context.
* CEP plugins can't use native host controls, so CEP dialogs and panels don't match host ones without a lot of CSS work. UXP plugins can use Spectrum UI components that look and feel like the host application.
* The ExtendScript side of CEP uses an old JavaScript version that lacks many modern features, so a developer has to juggle two different JavaScript environments. UXP uses a single, modern JavaScript engine throughout.

## Building with UXP

UXP supports the HTML and CSS you need to build simple panels, or, with the React JavaScript framework, complex panels, dialogs, and headless (no UI) plugins.

Because UXP communicates directly with the host application, the issues associated with the CEP and ExtendScript interface go away. In general, plugin development is simpler with UXP.

UXP comes with a plugin loader and debugger that make managing plugin development simpler than what's available in CEP.

UXP plugins can use [Spectrum CSS](https://opensource.adobe.com/spectrum-css/) components, which provide theme-aware, cross-platform user interfaces that look consistent across Creative Cloud applications.
