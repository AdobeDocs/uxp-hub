---
title: Build a Hybrid Plugin
description: Compile a C++ uxpaddon, connect it to JavaScript, configure platform binaries, debug both layers, and package the plugin.
keywords:
  - UXP Hybrid
  - Hybrid Plugins
  - C++
  - uxpaddon
  - building
  - manifest
  - debugging
  - packaging
contributors:
  - https://github.com/karan0207
---

# Build a Hybrid plugin

Build the native addon first, connect it to the UXP layer, then verify every target platform before packaging. This guide uses the SDK's headers and directory conventions throughout.

<InlineAlert variant="info" slots="text" />

Hybrid plugins require **Premiere 26.2 or later** or **Photoshop 24.2.0 or later**. Media Encoder and InDesign do not currently support them.

## Before you begin

Download and extract the [UXP Hybrid Plugin SDK](index.md#get-the-hybrid-plugin-sdk). You also need:

- Xcode on macOS or Visual Studio on Windows.
- A standard UXP plugin that loads in the target host.
- Access to each platform and architecture the plugin will support.

## 1. Build the uxpaddon

### Create a dynamic library project

- Create a new **Xcode** (macOS) or **Visual Studio** (Windows) project targeting a **dynamic library**.
- Add the UXP Hybrid Plugin SDK `src/api` path to your project's include paths.

### Register native exports

Include the main header and register your initialization and termination routines:

```cpp
#include "UxpAddon.h"

addon_value init(addon_env env, addon_value exports) {
    // Register functions and properties on 'exports'
    return exports;
}

void terminate() {
    // Cleanup logic
}

UXP_ADDON_INIT(init);
UXP_ADDON_TERMINATE(terminate);
```

In `init()`, use the addon APIs defined by `UxpAddonShared.h` to register functions and properties on `exports`. JavaScript can call anything added to that object.

### Compile the uxpaddon

Build a `.dylib` on macOS or `.dll` on Windows, then rename the output extension to `.uxpaddon`.

## 2. Load the addon from JavaScript

Once compiled, the addon is loaded with `require()` and its exported functions are available immediately:

```js
const addon = require("sample-uxp-addon.uxpaddon");
const result = addon.first_function();
```

The addon behaves like any other JavaScript module: you can call functions, read properties, and pass data back and forth between JavaScript and C++.

## 3. Configure the manifest

Hybrid plugins require a few specific settings in the [`manifest.json`](../../explanation/concepts/manifest/index.md):

- **Manifest version** 6 or above.
- The [`addon`](../../explanation/concepts/manifest/index.md#addon) field with the name of your uxpaddon.
- The [`enableAddon`](../../explanation/concepts/manifest/index.md#enableaddon) permission.

```json
{
  "manifestVersion": 6,
  "host": {
    "app": "premierepro",
    "minVersion": "26.2.0"
  },
  "addon": {
    "name": "sample-uxp-addon.uxpaddon"
  },
  "requiredPermissions": {
    "enableAddon": true
  }
}
```

## 4. Arrange the native binaries

The uxpaddon binaries must be placed in a specific directory layout within your plugin bundle, organized by platform and architecture:

```text
plugin-root/
|-- manifest.json
|-- main.js
|-- mac/
|   |-- x64/
|   |   `-- sample-uxp-addon.uxpaddon
|   `-- arm64/
|       `-- sample-uxp-addon.uxpaddon
`-- win/
    `-- x64/
        `-- sample-uxp-addon.uxpaddon
```

If the directory structure is incorrect, the plugin will fail to load with a _"Plugin Manifest Validation Failed"_ error in the UDT logs.

<InlineAlert variant="info" slots="text" />

Load the SDK's precompiled `template-plugin` in UDT to verify the host, SDK, and directory setup before compiling your own addon.

## 5. Schedule asynchronous work

Respect the uxpaddon threading model:

- **Initialization and termination** routines run on the **main thread** of the host application.
- **JavaScript calls** to addon functions run on a **dedicated scripting thread** (never the main thread).

When your addon needs to perform work on the main thread (e.g., interacting with host-specific APIs) and return results to JavaScript, you must use the asynchronous scheduling APIs. These operations are exposed to JavaScript as **Promises**.

The pattern involves three steps:

1. **From the scripting thread**: create a deferred promise and schedule work on the main thread.
2. **On the main thread**: perform the task, then schedule the result back to the scripting thread.
3. **Back on the scripting thread**: resolve or reject the promise.

The following pseudocode shows the queue handoff. Use the SDK's `MyAsyncEcho` implementation for complete types, state management, and error handling.

```cpp
addon_value MyAsyncOperation(addon_env env, addon_callback_info info) {
    // Extract and convert arguments to standard C++ types
    // (addon_value is transient and cannot be used after this function returns)
    std::string myArg = ExtractString(env, argValue);

    // Create a promise for the JavaScript caller
    addon_deferred promiseValue = nullptr;
    addon_value deferred = nullptr;
    apis.uxp_addon_create_promise(env, &deferred, &promiseValue);

    // Schedule work on the main thread
    apis.uxp_addon_schedule_on_main_queue(env, MainThreadTask, ...);
    return deferred;
}

void MainThreadTask(...) {
    // Perform the task on the main thread...

    // When done, schedule the result back to the scripting thread
    apis.uxp_addon_schedule_on_javascript_queue(env, ScriptThreadTask, ...);
}

void ScriptThreadTask(...) {
    apis.uxp_addon_open_handle_scope(env, &scope);

    // Resolve (or reject) the JavaScript promise
    addon_value resultValue = nullptr;
    apis.uxp_addon_resolve_deferred(env, deferred, resultValue);
    apis.uxp_addon_close_handle_scope(env, scope);
}
```

The `template-dev` project in the SDK includes a working implementation of this pattern (see `MyAsyncEcho` in `module.cpp`).

## Access files through the native addon

The C++ layer can use native file-system APIs and direct paths. A JavaScript path string does not bypass UXP permissions by itself; pass it to a function exported by the addon, which performs the native operation.

Assuming the addon exports a `processFile()` function:

```js
const addon = require("sample-uxp-addon.uxpaddon");
const result = await addon.processFile("/path/to/file.txt");
```

JavaScript code that accesses files directly remains subject to the standard UXP file-system model. See the [Filesystem recipe](../recipes/filesystem-operations/index.md) for sandbox storage, user-selected entries, and file permissions.

## 6. Debug both layers

Hybrid plugins have both a JavaScript and a C++ layer, each requiring its own debugging approach:

- **JavaScript**: use the UDT Debug tool to set breakpoints and inspect variables.
- **C++**: attach your IDE's debugger to the running host process (`Premiere` on Premiere, `Photoshop` on Photoshop). In most IDEs this is found under **Debug** > **Attach to Process**.

## 7. Package and distribute

Hybrid plugins follow the standard [distribution workflow](../distribution/overview/index.md), with additional requirements for native binaries. Complete the following checks before distributing the plugin.

### Verify the bundle layout

Make sure your `.uxpaddon` files use the [required platform and architecture layout](#4-arrange-the-native-binaries). The folder hierarchy is strict: if it doesn't match, the plugin will fail to load with a _"Plugin Manifest Validation Failed"_ error in UDT.

### Build every target architecture

Compile your uxpaddon for each supported platform:

- **macOS arm64** (Apple Silicon)
- **macOS x64** (Intel)
- **Windows x64**

Use native hardware, compatible virtual machines, or CI runners for platforms and architectures unavailable locally. Verify the final binaries on each supported architecture before distribution. For macOS universal binaries, see [Apple's guide](https://developer.apple.com/documentation/apple-silicon/building-a-universal-macos-binary).

<InlineAlert variant="warning" slots="text" />

You can package a Hybrid plugin with only a subset of architectures, but it will not load where a matching binary is absent. The **Creative Cloud Marketplace requires all three architectures** and rejects incomplete `.ccx` packages. Use partial architecture support only for controlled independent or enterprise distribution.

### Sign and notarize macOS binaries

The macOS `.uxpaddon` executables must be signed and notarized with a valid **Apple Developer ID** certificate. Requirements:

- Self-signed or test certificates are **not accepted**.
- The certificate must be valid for **at least one year**.
- Only the `.uxpaddon` binaries need signing. The rest of the plugin bundle (JavaScript, HTML, CSS, manifest) does not.

See the [FAQ](faq.md#do-i-need-an-apple-developer-id) for more details on Apple Developer ID requirements.

### Set the plugin ID

Before packaging, verify the `id` in `manifest.json`. For a **Creative Cloud Marketplace** release, obtain the ID from the [Developer Distribution portal](https://developer.adobe.com/developer-distribution/creative-cloud/docs/guides/plugin-id#starting-from-adobe-developer-distribution) and use the same value in the manifest.

### Package with UDT

In UDT, open the plugin's **Actions** (`•••`) menu and select **Package**. This creates a `.ccx` installer.

### Test the installer

Install the `.ccx` file on a clean system (or at least one without your development environment) to verify:

- The plugin installs without errors.
- The uxpaddon loads correctly on each supported platform.
- No security warnings appear (if binaries are properly signed).

<InlineAlert variant="warning" slots="text" />

Since Hybrid plugins include native code, users will be prompted for **OS administrator credentials** during installation and updates. This is expected behavior.

For the full distribution workflow (Marketplace submission, independent distribution, enterprise deployment), see the [Share & Distribute](../distribution/overview/index.md) section.
