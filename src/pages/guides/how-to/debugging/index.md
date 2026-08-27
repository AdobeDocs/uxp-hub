---
title: Debugging your Plugin
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

# Debugging your Plugin

Even carefully written plugins misbehave. When yours does, UXP gives you two ways to inspect what's happening, plus a short list of usual suspects to check first.

## Debugging tools

There are two ways to attach a debugger to a running plugin.

### UXP Developer Tool

For most plugins, the UXP Developer Tool (UDT) is the simplest option. Once your plugin is loaded, open the **Actions** (`•••`) menu for that plugin and choose **Debug**. UDT opens a DevTools window wired to your plugin, with a **Console** for logs and errors, an **Elements** panel for inspecting your HTML and applied CSS, and a **Sources** panel for setting breakpoints and stepping through code.

Two Actions-menu features make the day-to-day loop faster:

- **Watch** automatically reloads the plugin whenever you save a change to its JavaScript, HTML, or CSS.
- **Reload** reloads the plugin on demand when you'd rather do it yourself.

### Chrome DevTools

If you loaded your plugin from the command line, you can attach Chrome DevTools instead:

1. Open a new Chrome window and go to `chrome://inspect`.
2. Click **Configure...** next to *Discover network targets* and add `localhost:xxxx`, where `xxxx` is the port declared in your `debug.json` file.
3. Your plugin appears on the page by its ID once it's loaded. Click **inspect** to open the debugger.
4. In the **Sources** tab, enable **Pause on caught exceptions** so errors stop execution where they happen.

Chrome DevTools exposes more features than UDT, though many don't apply inside the UXP runtime. Pick whichever fits your workflow.

<InlineAlert variant="info" slots="text"/>

`console.log()` and friends write straight to whichever debugger you have attached, so sprinkling logs through your code is still a fast way to confirm what's running and with what values.

## Why doesn't my plugin work?

When a plugin won't load or run, it's usually one of these.

### Manifest problems

JSON is easier to read than it is to get right. A single misplaced bracket or missing comma in `manifest.json` can stop your plugin from loading. If nothing shows up in your host application, check UDT for load errors and run the file through a JSON linter (VS Code has one built in).

### Installation problems

Load your development plugin through the UXP Developer Tool. Copying files into an application directory by hand is unreliable and easy to get wrong; let UDT put the plugin where the host expects it.

### JavaScript problems

A JavaScript error almost always surfaces in the debugger console, often as a stack trace with the offending line number near the top. Read from the top of the trace down to your own code.

### CSS problems

The engine behind UXP covers common HTML and CSS, but not everything on the web platform, and some properties behave differently or are limited. When layout looks wrong, open the **Elements** panel, select the element, and review the applied rules under **Styles**. Selectively commenting out rules until the layout changes is a reliable way to isolate the culprit.

### Not watching

The **Watch** feature only reloads on file changes if you've turned it on from the Actions menu. If saved changes aren't showing up, confirm Watch is enabled, or reload the plugin manually.

### Gremlins

Occasionally, restarting both the host application and the UXP Developer Tool clears a problem that has no obvious cause. When nothing else explains it, try that before digging deeper.
