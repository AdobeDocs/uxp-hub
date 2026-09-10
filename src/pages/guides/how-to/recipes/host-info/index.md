---
title: Host Environment Information
description: Read the host application, UXP runtime, operating system, and locale so a plugin can adapt safely.
keywords:
  - host information
  - platform detection
  - version checking
  - os detection
  - locale
  - environment
contributors:
  - https://github.com/karan0207
---

# Read host environment information

Read environment information when behavior depends on the host application, UXP runtime, operating system, or locale. Keep platform and version branches narrow, and provide a fallback when a feature is unavailable.

<Fragment src="../_shared/prerequisites.md" />

## Choose the information source

UXP provides three main modules for host environment detection:

| Module      | Purpose                        | Key Properties                                                                        |
| :------------ | :-------------------------------- | :----------------------------------------------------------------------------------- |
| `host`      | Application and UI information | `name`, `version`, `uiLocale`                                                         |
| `versions`  | UXP runtime and plugin version | `uxp`, `plugin`                                                                       |
| `os`        | Operating system information   | `platform()`, `release()`, `arch()`, `cpus()`, `totalmem()`, `freemem()`, `homedir()` |

## Log host and runtime details

Import `host` and `versions` from `uxp`, and use the Node-style `os` module for operating-system details.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, text" />

#### index.js

```js
const { host, versions } = require("uxp");
const os = require("os");

function logHostInfo() {
  console.log("=== Host Environment ===");
  console.log(`OS: ${os.platform()} ${os.release()}`);
  console.log(`Application: ${host.name} v${host.version}`);
  console.log(`UXP Runtime: v${versions.uxp}`);
  console.log(`Plugin Version: v${versions.plugin}`);
  console.log(`UI Locale: ${host.uiLocale}`);
}
logHostInfo();
```

#### Console Output

```text
=== Host Environment ===
OS: darwin 24.6.0
Application: premierepro v25.6.0
UXP Runtime: vuxp-8.1.0-local
Plugin Version: v1.0.0
UI Locale: en_US
```

<InlineAlert variant="info" slots="text"/>

The `os.platform()` method returns `"darwin"` for macOS and `"win32"` for Windows. Use this to detect the user's operating system reliably.

## Branch by operating system

Use `os.platform()` when a path, URL scheme, shortcut, or native integration differs between macOS and Windows.

```js
const { host, shell } = require("uxp");
const os = require("os");

async function openMapsLocation(address) {
  const isMac = os.platform() === "darwin";
  let url;
  if (isMac) {
    // Use Apple Maps on macOS
    url = `maps://?address=${encodeURIComponent(address)}`;
  } else {
    // Use Bing Maps on Windows
    url = `bingmaps:?q=${encodeURIComponent(address)}`;
  }

  try {
    await shell.openExternal(url, "Opening maps application");
    console.log(`Opened maps for: ${address}`);
  } catch (error) {
    console.error("Failed to open maps:", error);
  }
}

// Example usage
openMapsLocation("345 Park Ave, San Jose");
```

## Gate behavior by version

Check the application or UXP version before calling an API unavailable in older installations. The following example performs a simple major-and-minor comparison:

```js
const { host, versions } = require("uxp");

function supportsAdvancedFeatures() {
  const uxpVersion = versions.uxp;

  // Parse version string (e.g., "8.1.0" -> 8.1)
  const majorMinor = parseFloat(
    uxpVersion.split(".").slice(0, 2).join(".")
  );

  // Check if UXP is version 8.1 or higher
  return majorMinor >= 8.1;
}

// Conditionally enable features based on version
function initializePlugin() {
  console.log("Initializing plugin...");

  if (supportsAdvancedFeatures()) {
    console.log("Advanced features enabled (UXP 8.1+)");
    // Enable newer API usage
  } else {
    console.log("Using legacy mode (UXP < 8.1)");
    // Provide fallback behavior
  }
}
```
