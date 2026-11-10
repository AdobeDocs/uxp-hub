---
title: UXP for ExtendScript Developers
description: What changes when you move from ExtendScript and the ExtendScript Toolkit to UXP.
keywords:
  - Creative Cloud
  - API Documentation
  - UXP
  - Plugins
  - JavaScript
  - ExtendScript
  - SDK
  - Scripting
contributors:
  - https://github.com/kasivn
---

# UXP for ExtendScript Developers

Documentation reviewed: November 4, 2026. Distinguish plugin examples from standalone UXP scripts, and verify APIs against your minimum host/runtime version.

If you're coming to UXP from ExtendScript and the ESTK (ExtendScript ToolKit) or its successor, the [ExtendScript Debugger](https://marketplace.visualstudio.com/items?itemName=Adobe.extendscript-debug), here's what's new.

ExtendScript wasn't unique to Photoshop, so most of what follows (modern JavaScript, development environment, UI, HTML support) applies no matter which host application you're moving from. The DOM access section below uses Photoshop as its example since that's the most common source of ExtendScript migrations; see the **Host Apps** menu at the top of this site for your host application's own DOM API reference if you're migrating from a different one. The `batchPlay` API and migration helper shown below are Photoshop-specific.

### Different DOM access

UXP provides different methods for accessing each host application's DOM. See your host's API reference for details. The entire DOM isn't yet exposed through UXP for every host application, but coverage grows with each release.

In Photoshop, [batchPlay](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true) can access operations not exposed directly by the Photoshop DOM API. `batchPlay` is not available in Premiere, InDesign, Media Encoder, and After Effects.

Host mutation and module-access rules also differ. InDesign 18.4+ exposes its DOM through `require("indesign")`; Premiere 26.3+ requires action creation under a project lock; Photoshop API v2 requires modal scope for state changes. Review the [cross-host differences](../cross-host-differences/index.md), rather than directly translating Photoshop code into another host.

### Scripts and asynchronous work

A UXP script does not have the same lifetime as a persistent plugin. InDesign scripts can finish before a pending asynchronous operation completes. Use the documented [global await pattern](https://developer.adobe.com/indesign/uxp/scripts/concepts/global-await/) to wait for required work. Do not assume that starting an async function without awaiting it will keep a script alive.

Other hosts can have different scripting and plugin contracts. Follow each API member's return type: many operations return Promises, but others are synchronous. Test script completion, errors, and result handling in the target application.

### A Photoshop migration helper for ExtendScript developers

If you use `executeAction` and `executeActionGet` often in Photoshop ExtendScript code, the [ExtendScript batchPlay logger](https://github.com/adobe-uxp/ps-es-to-uxp) utility can help. Plug the `ps-es-to-uxp` JSX code into your ExtendScript project, and it prints out your `executeAction` and `executeActionGet` calls in a format suitable for [batchPlay](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true).

### Development environment

ExtendScript Toolkit ("ESTK") was the development environment of choice for many years. It has largely been replaced by a Visual Studio Code extension, a widely used editor.

UXP source code (HTML, CSS, and JavaScript) can be developed in the editor of your choice, though many UXP developers prefer VS Code for its extensibility.

### User interface

Many ExtendScript scripts have little to no UI: the end user picks a script from a menu, and it runs without a visible interface. When an ExtendScript script does need a UI, it typically uses simple `alert()`, `confirm()`, and `prompt()` calls, or the more full-featured [ScriptUI](https://extendscript.docsforadobe.dev/user-interface-tools/scriptui-object-reference/).

UXP plugin UI uses supported HTML/CSS and JavaScript, or the available [Spectrum widgets and Spectrum Web Components](../../uxp-api/reference-spectrum/index.md). ScriptUI is not a portable UXP UI toolkit. Check which UI and dialog APIs your host supports for plugins versus scripts before rebuilding the interface.

Do not port `alert()`, `confirm()`, or `prompt()` unchanged. Since UXP 7.4, the common alert APIs are beta, require `featureFlags.enableAlerts` in a plugin's manifest, and are disabled in scripts. Their behavior also differs from blocking ExtendScript dialogs. See the [alert reference](../../uxp-api/reference-js/global-members/html-dom/alert.md) and your host's supported dialog alternatives.

### Modern JavaScript

ExtendScript uses an old version of JavaScript (ES3). UXP uses the V8 JavaScript engine, which supports ES6 and a number of features ExtendScript lacks. As an ExtendScript developer, you'll want to be familiar with modern ECMAScript syntax to follow UXP sample code. At minimum, it helps to understand:

* [`const` and `let` declarations, versus `var`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements)
* [Promises and asynchronous functions](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Async_JS)
* [Anonymous functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions)
* [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* [Template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
* [Maps](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

### What's different in UXP's HTML support

UXP is not a full browser, so common web CSS and HTML idioms may not work. Start with the shared [Unsupported Elements](../../uxp-api/reference-html/general/unsupported-elements.md) and [Unsupported Attributes](../../uxp-api/reference-html/general/unsupported-attributes.md) references, then verify behavior in the runtime bundled with your target host. See the [UI and dependency preflight](../uxp-for-cep-devs/technical-migration-guide/index.md#user-interface) before reusing browser libraries, Node.js modules, or a framework from a CEP extension.
