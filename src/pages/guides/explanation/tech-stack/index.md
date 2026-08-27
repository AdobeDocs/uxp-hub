---
title: Tech Stack Foundations
description: Essential web technologies, the UXP runtime, and debugging tools you need for UXP plugin development, across every host application.
keywords:
  - Learning resources
  - JavaScript
  - HTML
  - CSS
  - Debugging
  - JavaScript frameworks
contributors:
  - https://github.com/karan0207
---

# Tech Stack Foundations

## What you write in

A UXP plugin is built from the web technologies you already know, running on a single runtime that Adobe ships inside every UXP-enabled host application:

- **HTML** defines your panel's structure.
- **CSS** styles it. Adobe's Spectrum design system gives you components that match the host UI.
- **JavaScript** holds your logic and calls the host's APIs.

<InlineAlert slots="text" />

**UXP is not a standard browser environment.** Most tutorials assume you're using Web technologies in a browser or a server-side JS engine like Node.js. While UXP supports modern JavaScript syntax, not all HTML elements, CSS classes, or JavaScript APIs available in a browser will be available in UXP. Check the [UXP API reference](../../../uxp-api/index.md) for the exact elements, styles, and modules that are supported.

## The two surfaces

UXP work falls into two surfaces that share the same runtime:

- **Panels** render UI inside the host application. A panel is the visible, interactive part of a plugin.
- **Scripts** run logic against the host, with or without UI.

Because both use one engine, the code, libraries, and patterns you learn carry across panels, automation, and every other UXP-enabled Adobe application.

## Where to learn JavaScript, HTML, and CSS

If you're already comfortable with all three, skip ahead. If not, here's where to start, and what to come back to once you're writing real plugin code.

**Start here:**

- [Introduction to JavaScript](https://javascript.info/intro) and its [basics](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/JavaScript_basics)
- [JavaScript versions](https://www.w3schools.com/js/js_versions.asp) and [ECMAScript 2015 (ES6)](https://www.w3schools.com/js/js_es6.asp)
- [HTML basics](https://www.w3schools.com/html/html_intro.asp)
- [CSS basics](https://www.w3schools.com/css/css_intro.asp), [syntax](https://www.w3schools.com/css/css_syntax.asp), and [selectors](https://www.w3schools.com/css/css_selectors.asp)

**Come back to these once you're writing real plugin code:**

- [The DOM](https://eloquentjavascript.net/14_dom.html) and the global [window](https://www.w3schools.com/jsref/obj_window.asp) and [document](https://www.w3schools.com/jsref/prop_win_document.asp) objects
- [DOM events](https://javascript.info/introduction-browser-events)
- [CSS layout with Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Promises](https://javascript.info/promise-basics) and [async/await](https://javascript.info/async-await)
- [Traditional function syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function) vs. [arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

## Debugging

The [UXP Developer Tool](../../how-to/developer-tools/index.md#uxp-developer-tool-udt) (UDT) debugs plugins with the same engine as Chrome DevTools (CDT), so CDT skills carry straight over. If you haven't used it before, the [Chrome DevTools docs](https://developer.chrome.com/docs/devtools/overview/) cover the basics: [accessing the DOM](https://developer.chrome.com/docs/devtools/dom/), [modifying CSS](https://developer.chrome.com/docs/devtools/css/), and [debugging JavaScript with breakpoints](https://developer.chrome.com/docs/devtools/javascript/).

## Frameworks and tooling

Frameworks like [React](https://react.dev/), [Vue](https://vuejs.org/), [Angular](https://angular.io/), and [Svelte](https://svelte.dev/) all run in UXP panels. None of them are required: use one if you're already comfortable with it, or your panel's UI is complex enough to benefit from components and state management. For anything simpler, the plain `quick-starter` template covers it (`react-quick-starter` if you want React specifically).

Using a framework means adding build tooling on your machine. It compiles your framework code into plain JS/HTML/CSS before it loads into the plugin. UXP itself doesn't run Node.js or expose Node's APIs at runtime; Node here is strictly a local, build-time dependency.

- [Node.js](https://nodejs.org/en/): a **JavaScript runtime environment** you install locally to run `npm`/`yarn` and your framework's build tooling. Download the installer from the [Node.js download page](https://nodejs.org/en/download/) and run it; this also installs `npm`.
- [`npm`](https://www.npmjs.com): a **package manager** bundled with Node that manages your project's dependencies.
- [`yarn`](https://yarnpkg.com): an **alternative package manager** for Node. Install it with:

  ```bash
  npm install yarn --global
  ```

Static analysis tools like [ESLint](https://eslint.org/) catch common JavaScript problems before you run the code, and integrate with most [code editors](../../how-to/developer-tools/index.md#code-editor) and CI pipelines. See [Linting with ESLint](../../how-to/eslint/index.md) to set it up for UXP, and [TypeScript and IntelliSense](../../how-to/typescript/index.md) to add autocomplete and type checking, in plain JavaScript or TypeScript. Framework-specific ESLint plugins can also enforce that framework's best practices for you.

## Next steps

- Set up your [developer tools](../../how-to/developer-tools/index.md) if you haven't already.
- Ready to write code? Go to [Build Your First Plugin](../../tutorials/build-your-first-plugin/index.md).
