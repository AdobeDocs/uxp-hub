---
title: CEP to UXP Technical Migration Guide
description: API-by-API guidance for CEP developers migrating a Photoshop extension to UXP: entry points, native CEP functions, JS libraries, and ExtendScript.
keywords:
  - CEP
  - UXP
  - migration
  - CSInterface
  - ExtendScript
  - Vulcan
  - batchPlay
contributors:
  - https://github.com/kasivn
---

# CEP to UXP Technical Migration Guide

Documentation reviewed: November 4, 2026. Examples use manifest v5 unless stated otherwise. Match your plugin's minimum host version to the required [UXP runtime and API capabilities](../../../uxp-api/versions.md), and test in each target host on macOS and Windows.

This guide is geared towards CEP (Common Extensibility Platform) developers who would like more technical guidance on migrating their extensions to UXP (Unified Extensibility Platform). The migration process is no doubt challenging but will dramatically improve your development experience for future iterations of your plugins.

Most of this guide, including the JavaScript library and ExtendScript/DOM sections, uses Photoshop as its example host application, since that's where CEP-to-UXP migrations are most common. If you're migrating a CEP extension for a different host application, the [Migrating Native CEP Functions](#migrating-native-cep-functions) section below covers the APIs common across host apps (file I/O, network, opening external resources); check your host's own UXP API reference for its host-specific DOM API. `batchPlay` is Photoshop-specific and is not available in Premiere, InDesign, Media Encoder, and After Effects.

- [Why migrate to UXP?](#why-migrate-to-uxp)
  - [How CEP and UXP Differ](#how-cep-and-uxp-differ)
  - [Developer Tooling](#developer-tooling)
  - [Plugin Types](#plugin-types)
- [UXP Plugin Development](#uxp-plugin-development)
  - [Entrypoint Setup](#entrypoint-setup)
  - [User Interface](#user-interface)
- [Migrating Native CEP Functions](#migrating-native-cep-functions)
  - [Directory and File I/O Operations (File APIs)](#directory-and-file-io-operations-file-apis)
  - [Encoding API](#encoding-api)
  - [Opening Remote Resources](#opening-remote-resources)
  - [Process APIs](#process-apis)
- [Migrating CEP JavaScript Libraries](#migrating-cep-javascript-libraries)
  - [Vulcan Interface: Communicating across plugins within Photoshop](#vulcan-interface-communicating-across-plugins-within-photoshop)
  - [CS APIs: Communicating with your plugin and with Photoshop](#cs-apis-communicating-with-your-plugin-and-with-photoshop)
    - [Use Case: API Version (Photoshop)](#use-case-api-version-photoshop)
    - [Use Case: Sending/Receiving Events](#use-case-sendingreceiving-events)
    - [Use Case: Network Access](#use-case-network-access)
    - [Use Case: Customizing Menus](#use-case-customizing-menus)
    - [Use Case: Theme Support](#use-case-theme-support)
    - [Use Case: Localization](#use-case-localization)
    - [Use Case: Keyboard Events](#use-case-keyboard-events)
    - [Use Case: Adjusting Plugin Size](#use-case-adjusting-plugin-size)
- [Migrating ExtendScript/EvalScript to the Photoshop DOM API](#migrating-extendscriptevalscript-to-the-photoshop-dom-api)

There is no clear-cut path for migration, given that CEP and UXP are fundamentally different. CEP was based on CEF ([Chromium Embedded Framework](https://bitbucket.org/chromiumembedded/cef)), making it effectively act as a browser. UXP, on the other hand, is not a browser, and therefore complete feature parity is unlikely. This migration should serve as an opportunity to design a better, more performant version of your existing CEP extension, and perhaps a chance to add new features and support modern workflows. In fact, it is better to think of this process as a "reconstruction" with the benefits of UXP in mind, rather than a migration.

## Why migrate to UXP?

UXP provides a comprehensive extensibility solution, turning JavaScript and HTML markup into controls in native application windows. You can think of UXP as a small runtime with a subset of browser functionality that we intend to grow. It has a DOM, with auto-layout and CSS Flexbox layout capabilities. UXP can run JS using the platform-specific JS engine, laying out native controls on screen, and enabling C++ and JS hybrid app development. A unified, modern execution environment (JavaScript V8) allows plugin UI and business logic to share the same context, which means no need to use `evalScript` anymore!

### How CEP and UXP Differ

* CEP uses a full version of Chromium as a web host, which is resource intensive, especially since each CEP plugin runs in its own instance of Chromium. UXP plugins run in a single shared, lightweight engine.
* CEP doesn't talk directly to the host application. Host scripts are written in ExtendScript and passed to the app through EvalScript calls, so two different JavaScript engines are running at once. A plugin's code ends up artificially split between ExtendScript and JavaScript, and passing anything more than simple parameters between the two layers is awkward and inefficient. UXP plugins call host APIs directly from the same JavaScript context.
* CEP plugins can't use native host controls, so CEP dialogs and panels don't match host ones without a lot of CSS work. UXP plugins can use Spectrum UI components that look and feel like the host application.
* The ExtendScript side of CEP uses an old JavaScript version that lacks many modern features, so a developer has to juggle two different JavaScript environments. UXP uses a single, modern JavaScript engine throughout.

Because UXP communicates directly with the host application, plugins no longer need the CEP-to-ExtendScript bridge. For theme-aware interfaces, choose from [built-in Spectrum UXP widgets and Spectrum Web Components](../../../uxp-api/reference-spectrum/index.md). These are different implementations with different setup requirements; do not assume that the general Spectrum CSS library or browser components work unchanged in UXP.

### Developer Tooling

Here is the [CEP debugging guide](https://github.com/Adobe-CEP/CEP-Resources/blob/master/CEP_11.x/Documentation/Debugging%20Handbook.md). Here is the [UXP debugging guide](../../../guides/how-to/debugging/index.md). The [UXP Developer Tool](../../../guides/how-to/developer-tools/index.md) is a plugin loader and debugger, common to every UXP host, that makes plugin management during development easier.

### Plugin Types

In the CEP world, if you want the extension to appear in the Window --> Extensions menu, add the `<Menu>` tag. If you want the extension to launch on an event, specify those events using the `<StartOn>` tag. All CEP extension types must be specified in the manifest.xml file.

| CEP Extension Type | Specifications |
|:------ | :------ |
| Panel | Dockable, resizable, fly-out menus, re-opened at startup if open at shutdown |
| Modal Dialog | Opens a new extension window and forces the user to interact with it before returning control to the host application |
| Invisible/Custom | Extensions that never become visible during their lifecycle (CEP 5.0+) |
| Modeless | Opens a new extension window but does not force the user to interact with the extension window |

Meanwhile, UXP plugins are either commands or panels. Plugin metadata like the size, icons, label, and ID are defined in the `manifest.json` file. Plugin functionality and behavior is defined using the `entrypoints.setup()` API.

| UXP Plugin Type | Specifications |
|:------ | :------ |
| Panel | Dockable, resizable, fly-out menus, and theme-aware |
| Command | Direct action command that executes a custom task, triggered from the host application's Plugins menu, with optional UI |

Commands and panels are common UXP entry point types, but host and release support can differ, especially in beta releases. See [Panels and Commands](../../../guides/explanation/concepts/panels-and-commands/index.md) and your target host's documentation for modal dialogs and combining entrypoints in one plugin.

## UXP Plugin Development

### Entrypoint Setup

Declare entrypoint IDs and types in the manifest, then register their handlers and panel flyout menus with `require("uxp").entrypoints.setup()`. Call `setup()` once, during plugin initialization. The configuration consists of three handler maps:

1. **plugin**: This can be an object or a function. If this is a function, it is assumed as the `create` handler (described below).
   * `create()`: This is called after the plugin is loaded.
   * `destroy()`: This is called before the plugin is unloaded.
2. **panels**: Each key is a manifest panel ID and each value is an object or function. A function is treated as the `show` handler. An object must define either `create` or `show`. Supported asynchronous handlers may return a Promise; throw an exception or reject the Promise to signal failure. In manifest v5, panel handlers have these signatures:
  * `create(rootNode)`: Called when a panel is created.
  * `show(rootNode, data)`: Called to show a panel.
  * `hide(rootNode, data)`: Called to hide a panel.
  * `destroy(rootNode)`: Called when a panel is going to be destroyed.
  * `invokeMenu(menuId)`: Called when a panel flyout menu item is invoked.
   * `menuItems`: An array of menu items. Each item can be a string or an object with the properties defined below, in the same order as specified in this array. For specifying a separator, a value of "-" or a menu item with label "-" can be used at the required place in the array.
     * `{string}` id - identifier of the menu item.
     * `{string}` label - display text for the menu item. Should be localized. If label is not specified, id is used as label.
     * `{boolean}` enabled - enabled/disabled state for the menu item. Default - true.
     * `{boolean}` checked - checked state for the menu item. Default - false.
     * `{Array}` submenu - submenu for this menu item, again as an array of `menuItems`. The `id` of submenus should still be unique across the panel.
3. **commands**: This object contains a list of key-value pairs where each key is the command ID and each value is the command's data, whose type can be an object or function. If the command's data is a function, it is assumed to be the `run()` method.
  * `run(executionContext, ...arguments)`: In manifest v5, called when a command is invoked, including through supported inter-plugin communication.
  * `cancel()`: Reserved for future use in the common API reference. Do not rely on it for cancellation. Use the cancellation mechanism documented by your host API, such as Photoshop's modal execution context.

Manifest v4 panel handlers instead receive one `event` argument. Promise support and timeouts are method-specific; see the [entrypoints reference](../../../uxp-api/reference-js/modules/uxp/entry-points/entry-points.md). Verify panel show/hide behavior in the target host before using it to manage resources or assume visibility changes are always reported.

### User Interface

UXP plugins support a subset of HTML and CSS, along with [Spectrum UI options](../../../uxp-api/reference-spectrum/index.md). Review your CEP UI and dependencies before porting them:

- **Built-in Spectrum UXP widgets** need no npm installation. **Spectrum Web Components (SWC)** require installed and bundled UXP wrappers, manifest v5, and `featureFlags.enableSWCSupport`. SWC support starts in UXP 7.0; check the [SWC setup and supported versions](../../../uxp-api/reference-spectrum/swc/index.md), including the documented 0.37.0 component-version requirement for UXP 8.0.
- **Browser and Node.js dependencies** are not automatically compatible. UXP is neither a complete browser nor a full Node.js runtime. Check DOM assumptions, native addons, subprocess use, supported modules, and bundler output. The presence of `require("fs")` does not mean all Node.js APIs are available.
- **WebView** can host web content that cannot run in the native UXP UI, but adds browser-process overhead. Communicate with the plugin through the message bridge rather than accessing host APIs directly from the page. Local HTML rendering requires UXP 8.0 or later and the appropriate permissions. See the [WebView reference](../../../uxp-api/reference-js/global-members/html-elements/html-web-view-element.md).
- **Framework versions and accessibility** need testing on your minimum and current host versions. Verify keyboard navigation, dialogs, password inputs, and the chosen widget/SWC variants on both platforms. Do not infer current framework support from compatibility notes for an older UXP runtime.

## Migrating Native CEP Functions

There is a common set of APIs that plugins need access to, regardless of the host app. These include file I/O and network, among others. This section discusses the native CEP APIs extensions rely on, and guidelines for migrating those APIs to UXP.

### Directory and File I/O Operations (File APIs)

In CEP extensions, developers use the `window.cep.fs` object to perform directory and file I/O operations. CEP developers have arbitrary access to disk and machine resources, and arbitrary ability to launch processes (including bash scripts). Users do not have to consent to extensions accessing their file system. Because developers have full access, all file access is technically persistent. Secure storage, on the other hand, does not exist for CEP.

UXP offers two file APIs: entry-based `require("uxp").storage.localFileSystem` and path-based `require("fs")`. With manifest v5, choose the narrowest `requiredPermissions.localFileSystem` value that supports your workflow:

- `"plugin"` (default): access the plugin installation folder (read-only), data folder, and temporary folder.
- `"request"`: let the user select files or folders through pickers. Use returned entries for file operations and tokens where the receiving API requires them.
- `"fullAccess"`: access arbitrary paths within the current user's filesystem access rights, without a picker for each path. This is a broader declared permission, not a bypass for OS permissions or user consent requirements.

Use the [filesystem operations guide](../../../guides/how-to/recipes/filesystem-operations/index.md) to choose between these APIs and permissions. For reusable access to a picked location, see [persistent file access tokens](../../../uxp-api/reference-js/modules/uxp/persistent-file-storage/file-system-provider.md#createpersistenttokenentry); handle failures if the file moves, disappears, or is no longer accessible. Plugin data and temporary storage are not substitutes for irreplaceable user files. Use [secure storage](../../../uxp-api/reference-js/modules/uxp/key-value-storage/secure-storage.md) for suitable sensitive key/value data, not as the only copy of data that cannot be regenerated.

### Encoding API

CEP extensions can access the encoding API with the `window.cep.encoding` object. With this API, they can implement encoding while reading and writing file content. CEP developers can use UTF8 or Base64 encoding.

UXP's encoding options depend on which file API you use. The [`storage`](../../../uxp-api/reference-js/modules/uxp/persistent-file-storage/storage.md) module's `File.read()`/`write()` take a `format` Symbol, `formats.utf8` or `formats.binary`. The Node.js-style [`fs`](../../../uxp-api/reference-js/modules/fs/fs.md) module's `readFile()`/`writeFile()` instead take an `encoding` string literal: `"utf-8"`, `"utf-16be"`, or `"utf-16le"`.

### Opening Remote Resources

The `window.cep.util` object gives CEP developers access to `openURLInDefaultBrowser`, `registerExtensionUnloadCallback`, and `storeProxyCredentials`. In UXP, `require("uxp").shell.openExternal()` opens a web link in the system browser. The shell module does not provide direct equivalents for CEP's `registerExtensionUnloadCallback` and `storeProxyCredentials`; review lifecycle and authentication requirements separately.

Use `shell.openPath()` for local files and folders and `shell.openExternal()` for remote URLs or supported URI schemes. Declare the allowed schemes and file extensions in [manifest v5's launchProcess permission](../../../guides/explanation/concepts/manifest/index.md#launchprocess). Check your minimum host's shell support rather than treating a manifest version as proof that every host release implements the API.

### Process APIs

CEP developers can use the `window.cep.process` object to create (and maintain) processes. Here is the code to create a process and check if it is running:

```js
var result = window.cep.process.createProcess("/usr/X11/bin/xterm");

if (result.err === 0) {
  var pid = result.data;
  result = window.cep.process.isRunning(pid);
  if (result.data === true) {
    // running
  }
}
```

UXP does not provide a comprehensive CEP-style process-management API. `shell.openPath()` and `shell.openExternal()` launch files and URIs through system applications; they are not replacements for subprocess arguments, standard input/output, PID tracking, or process termination. The following manifest fragment declares permitted launches:

```json
{
  "requiredPermissions": {
    "launchProcess": {
      "schemes": ["https", "slack", "adobe"],
      "extensions": [".pdf", ".xd", ".psd"]
    }
  }
}
```

Both APIs are subject to runtime user consent. Await the result: the shell API resolves with an empty string on success or an error-message string on failure. Also handle rejected calls:

```js
async function openDocumentation() {
  const { shell } = require("uxp");
  try {
    const errorMessage = await shell.openExternal("https://developer.adobe.com/");
    if (errorMessage) {
      throw new Error(errorMessage);
    }
  } catch (error) {
    console.error("Could not open the documentation:", error);
    throw error;
  }
}
```

## Migrating CEP JavaScript Libraries

CEP JavaScript Libraries are counterparts of the Flex CSXS Library. They provide JavaScript APIs to access host application and CEP information. UXP allows each host application to expose APIs directly to the developer. This section looks specifically at what the Photoshop UXP API can provide to help you migrate.

### Vulcan Interface: Communicating across plugins within Photoshop

Vulcan is an Adobe library for communication across CEP extensions and host applications. It is not available in UXP. Where supported by the host, UXP's Plugin Manager can invoke another installed plugin's commands or show its panels within the same host application.

The requesting plugin must declare this manifest v5 permission:

```json
{
  "requiredPermissions": {
    "ipc": { "enablePluginCommunication": true }
  }
}
```

Find the responder by plugin ID, check that it is installed and enabled, then use `invokeCommand()` or `showPanel()` with an exposed entrypoint ID. Handle failures: the host may disallow a request. See [Communicate between plugins](../../../guides/how-to/inter-plugin-comm/index.md) for a complete example and verify support in your minimum host version.

| Type | CEP | UXP |
| :--- | :--- | :---|
| Plugin communication across host applications | Yes | Not provided by these APIs |
| Plugin communication within the same host application | Yes | Host-dependent; requires the IPC permission |

This is not a drop-in replacement for Vulcan's cross-application messaging. Inventory any cross-host workflow separately before committing to a migration architecture.

### CS APIs: Communicating with your plugin and with Photoshop

`CSInterface` is used to access information about the host application in which an extension is running, launch an extension, register interest in event notifications, and dispatch events. `CSInterface` helps with theming in Photoshop as well as customizing menus (fly-out and context menu).

#### Use Case: API Version (Photoshop)

This section applies to Photoshop. API versioning and modal execution differ by host; consult your target host's manifest and API documentation.

In CEP, you need to check the version tag of the CEP JavaScript APIs against the version of CEP integrated by Photoshop to make sure the API you want to use is available. `CSInterface.getCurrentAPIVersion()` is a method that retrieves the version of CEP integrated by Photoshop.

In UXP, the Photoshop version that a plugin is targeting needs to be specified in the manifest configuration. Additionally, you can specify the Photoshop API version the plugin should target.

The v1 implementation (Photoshop 2021) does not allow plugins to run in isolation, thereby allowing any plugin to modify the Photoshop state at any time. Photoshop 2022 introduced a modal JS scope, allowing plugins to run in isolation and guaranteeing against plugins modifying Photoshop at the same time.

What changes were made for v2?

* State-changing operations require a modal scope through [executeAsModal](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/executeasmodal), which also exposes history suspension and cancellation controls. See the [migration example below](#migrating-extendscriptevalscript-to-the-photoshop-dom-api).
* Updated Photoshop DOM access. See the [changelog](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/changelog/#updated-dom-api).

Photoshop 2021 supports only API v1; Photoshop 2022 introduced API v2 alongside v1. The manifest field is `host.data.apiVersion`. If omitted, its default depends on the plugin's **minimum targeted Photoshop version** (`host.minVersion`), not the version currently running:

* A plugin whose `host.minVersion` is less than `23.0.0` defaults to Photoshop API v1.
* A plugin whose `host.minVersion` is `23.0.0` or greater defaults to Photoshop API v2.

Use API v2 for new migrations; API v1 is deprecated. Set the value explicitly rather than relying on a default. For example, this Photoshop host configuration targets manifest v5-capable Photoshop versions:

```json
{
  "manifestVersion": 5,
  "host": {
    "app": "PS",
    "minVersion": "23.3.0",
    "data": { "apiVersion": 2 }
  }
}
```

This is a manifest fragment, not a complete plugin manifest. See the [Photoshop-specific manifest reference](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/manifest-v4/photoshop-manifest/#apiversion). Manifest version, Photoshop API version, and UXP runtime version are separate compatibility settings.

The following version accessors are common UXP APIs, independent of Photoshop's `apiVersion`:

**Access host application version**: `require("uxp").host.version`

**Access UXP API version**: `require("uxp").versions.uxp`

**Access plugin version from manifest**: `require("uxp").versions.plugin`

#### Use Case: Sending/Receiving Events

CSXS/CEP events are used to send events among extensions in an application, and among extensions in different applications.

**Host DOM events**: Each host application exposes its own DOM events and notification APIs. Consult your target host's UXP API reference for the events it supports.

**UXP lifecycle events**: UXP lifecycle events help manage a plugin's lifecycle. Register handlers using `entrypoints.setup()`. Host implementations can have limitations: for example, historical Photoshop issues affect recurring panel show/hide notifications. Verify behavior in your minimum and current target versions before using these callbacks to infer panel visibility. See [Known Issues](../../../uxp-api/known-issues.md#events).

The following are plugin lifecycle events your plugin can listen for:

* `uxpcreateplugin`: Indicates that the plugin's JS context has been created and that the plugin has been loaded successfully.
* `uxpdestroyplugin`: Indicates that the plugin's JS context is about to be destroyed.

The following are panel lifecycle events your plugin can listen for:

* `uxpcreatepanel`: Indicates that a panel is about to be created. This is called when a panel is first displayed, and each time after it is destroyed.
* `uxpdestroypanel`: Indicates that a panel is about to be destroyed.
* `uxpshowpanel`: Intended to indicate that a panel is about to be shown and should render into the attached node. Recurring notifications depend on host implementation.
* `uxphidepanel`: Indicates that a panel is hidden.
* `uxpinvokepanelmenu`: Indicates that a panel menu item has been invoked.

For commands, register a `run` handler. Do not assume a `cancel` callback or a command-cancel event provides a portable cancellation mechanism; use your host API's documented controls.

When moving from manifest v4 to v5:

1. Supported entrypoint methods can return Promises; this does not mean every handler must be async.
2. V4 panel handlers receive one `event` argument. In v5, panel `create`/`destroy` receive `rootNode`, and `show`/`hide` receive `rootNode, data`. Command `run` instead receives `executionContext, ...arguments`.
3. The common API reference documents a default 300 millisecond timeout for panel `create`, `show`, and `hide` from manifest v5 onward. This is not a limit on all async plugin work. Check the [entrypoints reference](../../../uxp-api/reference-js/modules/uxp/entry-points/entry-points.md) and host documentation for each method's contract.

#### Use Case: Network Access

Networking is a common UXP capability available on every host. CEP used to leverage CSInterface.js to get network information. Network access in UXP requires you to define the domains the plugin will access in the manifest. You can do this by adding the network object to the `requiredPermissions` section of the manifest.

UXP supports XMLHttpRequest, WebSockets, and Fetch APIs to perform network requests.

Limitations:

* On macOS, it is not possible to use self-signed certificates with secure WebSockets.
* WebSockets do not support extensions.

#### Use Case: Customizing Menus

CSInterface has two APIs that can be used for customizing flyout menus. Refer to [these notes](https://github.com/Adobe-CEP/CEP-Resources/blob/master/CEP_11.x/Documentation/CEP%2011.1%20HTML%20Extension%20Cookbook.md#fly-out-menu) for an example of how to configure content for flyouts, and how to use them in tandem with event listeners.

UXP support and limitations for menus vary by host. Where supported, define panel flyout menus in `entrypoints.setup()` under `panels.<panelId>.menuItems`. Each item's `label` is its displayed text; selections call `invokeMenu(menuId)`. Separately, `entrypoints[].label` in the manifest labels the command or panel in the host's Plugins menu. See the [Photoshop flyout-menu example](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/uxp-misc/flyout-menus/#flyout-menus).

#### Use Case: Theme Support

CSInterface uses the theme manager interface and CSInterface to update the extension theme after the host application's theme changes. CSInterface does this by listening for the CSXS event `com.adobe.csxs.events.ThemeColorChanged`. CEP would access the latest host theme information using `var skinInfo = JSON.parse(window.__adobe_cep__.getHostEnvironment()).appSkinInfo;`. In UXP, use the chosen Spectrum widgets or SWC theme support and supported CSS media queries; verify theme changes in each target host rather than porting CEP's theme listener unchanged.

#### Use Case: Localization

CEP uses host environment information provided in the manifest to load and update the extension. This guide details [CEP support](https://github.com/Adobe-CEP/CEP-Resources/blob/master/CEP_9.x/Documentation/CEP%209.0%20HTML%20Extension%20Cookbook.md#localization) for localization. UXP localization support varies by host. In Photoshop, you can [retrieve locale information](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp_guide/uxp-misc/localization-and-platforms/) for the host environment and localize plugin menu and panel labels in the manifest. Consult your target host's documentation for its localization support.

#### Use Case: Keyboard Events

CEP allows you to register an interest in specific keyboard events to prevent them from being sent directly to the host application, allowing you to implement your own callback functions. UXP handles keyboard events and shortcuts differently by host. In Photoshop, a plugin can set focus on a control inside a panel and listen for several types of keyboard presses, but it cannot globally override host shortcuts. See the [Photoshop forum discussion](https://forums.creativeclouddeveloper.com/t/how-to-set-a-keyboard-shortcut-for-a-photoshop-uxp-plugin/3236) for that host; consult your target host's documentation for its limitations.

#### Use Case: Adjusting Plugin Size

For modal and modeless CEP extensions, using `window.__adobe_cep__.resizeContent()` takes two parameters (width and height) and resizes the extension's content to the specified dimensions. The width and height parameters are expected to be unsigned integers. Extension min/max size constraints as specified in the manifest file apply and take precedence. If the specified size is out of the min/max size range, the min or max bounds will be used.

UXP plugins are restricted to the sizes defined by the manifest (`minimumSize`, `maximumSize`, `preferredDockedSize`, `preferredFloatingSize`) and cannot control the size of their panel programmatically — this manifest-based sizing model is common across UXP hosts and is the deliberate replacement for CEP's `window.__adobe_cep__.resizeContent()`. The manifest fields are identical everywhere, but each host renders and docks panels using its own UI shell, so exact visual behavior and minimum practical sizes can differ by host. The expectation is that plugin developers will design their UI in a responsive manner, allowing the user to configure the panel to their liking. If your plugin consists of multiple accordions in the UI, you might want to consider shipping with multiple panels instead. Then the user can group all those panels together, resize them, reorder them, collapse them, etc., as defined in the manifest.

## Migrating ExtendScript/EvalScript to the Photoshop DOM API

This section applies to Photoshop. `batchPlay` and the `executeAction` mapping described here are not available in Premiere, InDesign, Media Encoder, and After Effects.

JSX files define functions and objects to be executed in Photoshop's ExtendScript environment. These are executed in CEP either at plugin load time or using `evalScript`. You specify the path to JSX files in the `<ScriptPath>` node in manifest.xml.

ExtendScript calls to Photoshop were synchronous. Many Photoshop UXP methods return Promises: await them, or chain them with `.then()`, before using their results. Other methods and properties are synchronous. Follow each API member's documented signature rather than assuming every UXP call is asynchronous or cannot block execution.

In UXP, you cannot load and execute JSX files directly. Instead, you can access the Photoshop DOM directly using the Photoshop DOM API. If the current implementation does not fulfill your needs, you can use [`batchPlay`](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true) to execute Photoshop actions.

With Photoshop API v2, operations that modify Photoshop state must run inside `core.executeAsModal()`. This includes state-changing `batchPlay` operations; using `batchPlay` is not a way around modal execution. For example:

```js
async function createMigratedDocument() {
  const { app, core } = require("photoshop");
  try {
    await core.executeAsModal(async () => {
      await app.createDocument({ width: 512, height: 512 });
    }, { commandName: "Create migrated document" });
  } catch (error) {
    console.error("Could not create the document:", error);
    throw error;
  }
}
```

Await both the modal call and asynchronous work inside it so the scope does not end early. For long-running work, use the modal execution context's cancellation controls. History suspension and rollback require their documented handling; modal scope alone is not an automatic transaction or rollback guarantee. See [Modal Execution](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/executeasmodal) for cancellation, history, and version-specific timeout behavior.

`batchPlay` is the evolution of `executeAction` from ExtendScript. Where `executeAction` could only play one descriptor at a time, `batchPlay` accepts an array of action descriptors. In ExtendScript, we provided a class around constructing descriptors, references, and putting values in. With `batchPlay`, we have replaced these related classes with `actionJSON`. If you have used `executeAction` in ExtendScript, you may recall 4-character codes (OSTypes) and helper methods around them. In `actionJSON`, we instead use extended string identifiers such as `colorSampler`. You can still use an OSType by pre-pending it with a `$` sign and passing that as a string, like `$app`.
