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
title: UXP for ExtendScript Developers
description: What changes when you move from ExtendScript and the ExtendScript Toolkit to UXP.
---

# UXP for ExtendScript Developers

If you're coming to UXP from ExtendScript and the ESTK (ExtendScript ToolKit) or its successor, the [ExtendScript Debugger](https://marketplace.visualstudio.com/items?itemName=Adobe.extendscript-debug), here's what's new.

### Different DOM access

UXP provides different methods for accessing the host application's DOM. See the [Photoshop UXP API reference](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/?aio_external=true) for details. The entire DOM isn't yet exposed through UXP for every host application, but coverage grows with each release.

As a workaround for anything not yet exposed directly, Photoshop provides a feature called [batchPlay](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true).

### A migration helper for ExtendScript developers

If you use `executeAction` and `executeActionGet` often in your code, the [ExtendScript batchPlay logger](https://github.com/adobe-uxp/ps-es-to-uxp) utility can help. Plug the `ps-es-to-uxp` jsx code into your ExtendScript project, and it prints out your `executeAction` and `executeActionGet` calls in a format suitable for the UXP equivalent, [batchPlay](https://developer.adobe.com/photoshop/uxp/2022/ps-reference/media/batchplay?aio_external=true).

### Development environment

ExtendScript Toolkit ("ESTK") was the development environment of choice for many years. It has largely been replaced by a Visual Studio Code extension, a widely used editor.

UXP source code (HTML, CSS, and JavaScript) can be developed in the editor of your choice, though many UXP developers prefer VS Code for its extensibility.

### User interface

Many ExtendScript scripts have little to no UI: the end user picks a script from a menu, and it runs without a visible interface. When an ExtendScript script does need a UI, it typically uses simple `alert()`, `confirm()`, and `prompt()` calls, or the more full-featured [ScriptUI](https://creativepro.com/files/kahrel/indesign/scriptui.html).

In UXP, you design as simple or as complex a UI as you want, using HTML and CSS for the visual part and JavaScript for the logic behind it.

### Modern JavaScript

ExtendScript uses an old version of JavaScript (ES3). UXP uses the V8 JavaScript engine, which supports ES6 and a number of features ExtendScript lacks. As an ExtendScript developer, you'll want to be familiar with modern ECMAScript syntax to follow UXP sample code. At minimum, it helps to understand:

* [`const` and `let` declarations, versus `var`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements)
* [Promises and asynchronous functions](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Async_JS)
* [Anonymous functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions)
* [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
* [Template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
* [Maps](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

### What's different in UXP's HTML support

UXP provides an HTML interpreter similar to Chromium, but more limited than a full browser, so some common web CSS and HTML idioms don't work. See [Unsupported Elements and Attributes](https://developer.adobe.com/photoshop/uxp/2022/guides/uxp-guide/unsupported/?aio_external=true) for the current list.
