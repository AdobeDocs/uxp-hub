---
keywords:
  - Spectrum UXP Reference
  - Spectrum Web Components
  - SWC
  - Web Components
  - Spectrum differences
  - Spectrum in UXP
  - UI
  - User interface
title: Building User Interfaces in UXP
description: Learn about the three ways to create user interfaces in UXP plugins
contributors:
  - https://github.com/karan0207
---

# User Interfaces

Learn the three ways to build user interfaces for your UXP plugins, and when to reach for each one.

## Overview

Every UXP plugin with a visual component needs a user interface. Whether you're building a simple dialog or a complex panel, UXP gives you multiple ways to create UI that looks and feels like a native part of the host application.

UXP provides three approaches for building user interfaces:

1. [**Spectrum Web Components (SWC)**](#spectrum-web-components-swc): Adobe's modern Web Component library, and the recommended choice for most plugins.
2. [**Spectrum UXP Widgets**](#spectrum-uxp-widgets): built-in, Adobe-styled components that work out of the box with zero setup.
3. [**Standard HTML Elements**](#standard-html-elements): familiar web technologies with full control through custom CSS.

<InlineAlert variant="info" slots="heading, text" />

Recommended approach

Each technology has its strengths, and you can mix and match them in the same plugin. For anything you plan to ship, **we recommend Spectrum Web Components** for the most complete feature set and future support.

## Understand the Options

Before the technical details, it helps to know some key terminology:

- [**Spectrum**](https://spectrum.adobe.com/): Adobe's open-source design language[^1] and guidelines that ensure consistency across applications.
- [**Web Components**](https://developer.mozilla.org/en-US/docs/Web/API/Web_components): A set of HTML5 technologies that let you create custom, reusable HTML elements.
- [**Adobe Spectrum Web Components**](https://opensource.adobe.com/spectrum-web-components/) (SWC): An open-source library of pre-built Web Components styled according to Spectrum design guidelines.

[^1]: Adobe is working on the second iteration of the Spectrum design system; more information can be found [here](https://s2.spectrum.adobe.com/).

### Spectrum Web Components (SWC)

[Spectrum Web Components](https://opensource.adobe.com/spectrum-web-components/) are Adobe's official implementation of the Spectrum Design System using modern [Web Component standards](https://developer.mozilla.org/en-US/docs/Web/API/Web_components). They offer the most complete feature set and the closest adherence to Spectrum guidelines.

To use SWCs, you need to [install each component](https://opensource.adobe.com/spectrum-web-components/getting-started/) individually via `npm` or `yarn` and import it in your code:

```bash
npm install @spectrum-web-components/button@0.37.0
npm install @spectrum-web-components/textfield@0.37.0
```

<InlineAlert variant="info" slots="heading, text" />

Version Requirement

Some hosts currently require a pinned version of Spectrum Web Components. For Premiere plugins, **lock to version 0.37.0** for the time being; check your host's release notes for its current requirement.

Then import and use them in your JavaScript:

```javascript
import '@spectrum-web-components/button/sp-button.js';
import '@spectrum-web-components/textfield/sp-textfield.js';
```

```html
<sp-button variant="primary">I'm a SWC button</sp-button>
<sp-textfield placeholder="Enter your name"></sp-textfield>
```

| Best for                                                                                                                               | Trade-offs                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| **Production plugins** that need the **full range of Spectrum components**, or when you need to inspect and debug component internals. | Requires Node.js, a package manager (npm/yarn), and a bundler (Webpack, Rollup, etc.) to build your plugin. |

You'll also need to enable SWC in your `manifest.json`:

```json
{
  "featureFlags": {
    "enableSWCSupport": true
  }
}
```

### Spectrum UXP Widgets

Spectrum UXP Widgets are built directly into the UXP platform. They provide ready-to-use, Adobe-styled components that automatically match the host application's look and feel, including dark and light theme support.

```html
<sp-button variant="primary">I'm a Spectrum button</sp-button>
<sp-textfield placeholder="Enter your name"></sp-textfield>
<sp-checkbox>Enable feature</sp-checkbox>
```

These widgets are immediately available: no installation or imports required. Just use them like any other HTML tag.

| Best for                                                                                                      | Trade-offs                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Quick prototyping**, getting started with UXP, or when you want the host application's native look without extra setup. | **Limited number of components** available. \<br/\>**You can't customize their behavior** beyond the provided API or easily inspect their internal structure. |

### Standard HTML Elements

These are the familiar HTML elements you've likely used before: `<div>`, `<button>`, `<input>`, `<img>`, `<dialog>`, and more. They follow web standards and give you complete control over styling through CSS.

```html
<div class="container">
  <button class="primary-button">Click me</button>
  <input type="text" placeholder="Enter text" />
</div>
```

| Best for                                                                              | Trade-offs                         |
| --------------------------------------------------------------------------------------- | ------------------------------------ |
| Building **highly customized interfaces** that need to match a custom or non-Spectrum design system | Complex and expensive to implement |

<InlineAlert variant="warning" slots="heading, text" />

UXP is not a browser

While UXP supports modern web technologies, it's not a full browser environment. Not all HTML elements, CSS properties, or JavaScript APIs available in browsers will work in UXP.

## Mix and Match

The best part? You can combine all three approaches in a single plugin. UXP fully supports mixing Spectrum Web Components, Spectrum UXP Widgets, and standard HTML elements:

```html
<form>
  <!-- Standard HTML element -->
  <div class="section">
    <!-- Spectrum Web Component -->
    <sp-banner>
      <div slot="header">Welcome</div>
      <div slot="content">This is a mixed UI example</div>
    </sp-banner>

    <!-- Spectrum UXP Widget -->
    <sp-button variant="primary">Submit</sp-button>
  </div>
</form>
```

This flexibility lets you use the right tool for each part of your interface.

## Which Approach Should You Choose?

Your choice depends on your project requirements, timeline, and experience level.

### Use Spectrum Web Components if you:

- Are building a plugin you intend to ship
- Need access to the full Spectrum component library
- Want to inspect and debug component internals
- Are comfortable with npm and build tools

### Use Spectrum UXP Widgets if you:

- Want to prototype quickly with zero configuration
- Need a simple UI with standard components
- Want the host's native look without a build step

### Use Standard HTML Elements if you:

- Need highly custom UI components
- Have specific design requirements beyond Spectrum
- Are comfortable writing custom CSS
- Want maximum control over styling and behavior

For most projects, **we recommend Spectrum Web Components**. Reach for UXP Widgets when you want zero setup for a prototype or a simple panel, and standard HTML elements for very specific custom needs. UXP Widgets are still supported but may be deprecated in the future, so prefer SWC for anything you plan to ship.

## Working with JavaScript Frameworks

Frameworks aren't a fourth UI approach: they sit on top of the three above, most often layered over Spectrum Web Components. While vanilla JavaScript, HTML, and CSS are sufficient for many plugins, complex applications can benefit from modern JavaScript frameworks. UXP plugins work well with popular frameworks like **React**, **Vue**, and **Svelte**.

These frameworks help you manage complex state, create reusable components, and build more maintainable code. However, they require additional setup including Node.js, package managers, and build tools.

If you're already familiar with React or plan to build a complex plugin, the most popular approach is to use [React Wrappers for Spectrum Web Components](https://opensource.adobe.com/spectrum-web-components/using-swc-react/).

## Visual language and the Marketplace

You are free to use your own visual language in UXP plugins. At the moment, products sent for approval to the Creative Cloud Marketplace don't need to implement the Adobe Spectrum Design System, although we recommend it for consistency.

Best UX practices, as well as adherence to the Adobe Brand Guidelines, will be enforced during the approval process.
