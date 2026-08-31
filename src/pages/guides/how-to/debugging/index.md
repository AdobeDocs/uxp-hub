---
title: Debug Your Plugin
description: Debug UXP plugins with the UXP Developer Tool and Chrome DevTools, and work through the most common reasons a plugin fails to load or run.
keywords:
  - Debugging
  - UDT
  - UXP Developer Tool
  - Chrome DevTools
  - Console
  - Troubleshooting
contributors:
  - https://github.com/karan0207
---

# Debug your plugin

Use the UXP Developer Tool (UDT) debugger to inspect a running plugin, read errors, test layout changes, and step through JavaScript. Chrome DevTools is also available when you load a plugin from the command line.

## Debug with UDT

After loading the plugin, open its **Actions** (`•••`) menu and select **Debug**. UDT opens a DevTools window connected to the plugin:

- Use **Console** to read logs and runtime errors.
- Use **Elements** to inspect HTML and applied CSS.
- Use **Sources** to set breakpoints and step through JavaScript.

Use the other Actions-menu controls to shorten the edit-and-test loop:

- **Watch** automatically reloads the plugin whenever you save a change to its JavaScript, HTML, or CSS.
- **Reload** reloads the plugin on demand when you'd rather do it yourself.

## Debug with Chrome DevTools

If you loaded your plugin from the command line, you can attach Chrome DevTools instead:

1. Open a new Chrome window and go to `chrome://inspect`.
2. Click **Configure...** next to *Discover network targets* and add `localhost:xxxx`, where `xxxx` is the port declared in your `debug.json` file.
3. After the plugin loads, find it by its ID and select **inspect**.
4. In the **Sources** tab, enable **Pause on caught exceptions** so errors stop execution where they happen.

Chrome DevTools exposes additional browser tooling, although not every feature applies to the UXP runtime.

<InlineAlert variant="info" slots="text"/>

Calls to `console.log()`, `console.warn()`, and `console.error()` appear in the attached debugger. Use them to confirm which code ran and inspect relevant values.

## Troubleshoot common problems

Start with the symptom that matches what you see.

### The plugin does not load

A misplaced bracket or missing comma in `manifest.json` can prevent loading. Check UDT for load errors, then use VS Code's JSON diagnostics to locate syntax problems.

### The plugin is not available in the host

Load development plugins through UDT. Do not copy plugin files into an application directory manually; UDT registers the plugin with the selected host application.

### The plugin loads but does not work

Check the debugger console for JavaScript errors. Start at the top of the stack trace, then follow it to the first frame in your plugin code.

### The layout or styling is incorrect

UXP supports a defined subset of web HTML and CSS, and some properties behave differently from a browser. In **Elements**, select the affected element and inspect its applied rules under **Styles**. Disable rules one at a time to isolate the declaration causing the problem.

### Saved changes do not appear

Confirm that **Watch** is enabled in the Actions menu, or reload the plugin manually. Changes to `manifest.json` require you to unload and load the plugin again.

### The problem remains after reloading

Restart both the host application and UDT to clear stale plugin state. If the problem returns, reproduce it with the debugger open and inspect the first reported error.
