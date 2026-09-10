---
title: Recipes
description: Copy-ready examples for common UXP API, interface, code-organization, and debugging tasks.
keywords:
  - UXP recipes
  - code examples
  - clipboard operations
  - CSS styling
  - debugging
  - external processes
  - filesystem operations
  - host information
  - HTML elements
  - event listeners
  - JavaScript modules
  - network operations
contributors:
  - https://github.com/karan0207
---

<Superhero slots="heading, text" variant="centered" textColor="white" background="linear-gradient(135deg, #30186E 0%, #6432C8 100%)"/>

# Recipes

Find a focused example for the task at hand. Each recipe identifies required permissions or compatibility constraints before the code that depends on them.

## Work with platform APIs

<DiscoverBlock slots="link, text" width="300px"/>

[Filesystem](filesystem-operations/index.md)

Read and write files using sandbox storage, user-selected entries, or broader file-system permissions.

<DiscoverBlock slots="link, text" width="300px"/>

[Networking](network/index.md)

Request remote data with `fetch()` or XHR, and open persistent WebSocket connections.

<DiscoverBlock slots="link, text" width="300px"/>

[External processes](external-process/index.md)

Open files, launch registered URL schemes, and understand the limits of external process access.

<DiscoverBlock slots="link, text" width="300px"/>

[Clipboard](clipboard/index.md)

Read, write, and transform text while requesting only the clipboard access the plugin needs.

<DiscoverBlock slots="link, text" width="300px"/>

[Host information](host-info/index.md)

Inspect the host application, UXP runtime, operating system, and locale.

## Build the interface

<DiscoverBlock slots="link, text" width="300px"/>

[CSS styling](css-styling/index.md)

Apply styles, respond to host theme changes, and account for UXP CSS limitations.

<DiscoverBlock slots="link, text" width="300px"/>

[HTML elements](html-elements/index.md)

Define interface elements in markup or create them dynamically with JavaScript.

<DiscoverBlock slots="link, text" width="300px"/>

[Events and listeners](html-events/index.md)

Handle clicks, input changes, keyboard actions, and Spectrum component events.

## Organize and troubleshoot code

<DiscoverBlock slots="link, text" width="300px"/>

[JavaScript modules](js-modules/index.md)

Split plugin logic across CommonJS modules with `require()` and `module.exports`.

<DiscoverBlock slots="link, text" width="300px"/>

[Debugging techniques](debug/index.md)

Inspect values with console output and use temporary dialogs for quick checks.

## Other resources

Looking for plugin lifecycle topics instead, like commands, panels, or modal dialogs? See [How-to Guides](../index.md).
