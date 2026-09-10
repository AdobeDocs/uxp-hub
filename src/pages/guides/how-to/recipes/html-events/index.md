---
title: HTML Events and Listeners
description: Handle clicks, input changes, keyboard actions, and Spectrum component events in UXP interfaces.
keywords:
  - events
  - event listeners
  - click
  - addEventListener
contributors:
  - https://github.com/karan0207
---

# Handle HTML events

UXP supports familiar events for clicks, input changes, focus, forms, and keyboard actions. Attach listeners in JavaScript by default; inline HTML handlers require a broader manifest permission.

<Fragment src="../_shared/prerequisites.md" />

## Attach listeners in JavaScript

Use `addEventListener()` when an element may have multiple listeners or when you need to remove a listener later. Event-handler properties such as `onclick` are suitable for one handler at a time.

<CodeBlock slots="heading, code" repeat="2" languages="HTML, JavaScript" />

#### index.html

```html
<button id="firstButton">Click me</button>
<button id="secondButton">Click me too</button>
```

#### index.js

```js
// Method 1: addEventListener (recommended)
const firstButton = document.getElementById("firstButton");
firstButton.addEventListener("click", handleClick);

// Method 2: Event handler property
const secondButton = document.getElementById("secondButton");
secondButton.onclick = handleClick;

function handleClick(event) {
  console.log(`Button clicked: ${event.target.id}`);
}
```

<InlineAlert variant="info" slots="text"/>

Using `addEventListener()` is preferred because it allows multiple event listeners on the same element and provides better control over event handling.

## Use inline handlers only when required

An inline handler places JavaScript in an HTML attribute. Enable `allowCodeGenerationFromStrings` before using this pattern.

<CodeBlock slots="heading, code" repeat="3" languages="HTML, JavaScript, JSON" />

#### index.html

```html
<button id="thirdButton" onclick="handleClick(event)">Click me</button>
```

#### index.js

```js
function handleClick(event) {
  console.log(`Button clicked: ${event.target.id}`);
}
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "allowCodeGenerationFromStrings": true
  }
}
```

<InlineAlert variant="warning" slots="heading, text"/>

Security consideration

The `allowCodeGenerationFromStrings` permission allows code execution from strings, which can introduce security risks. Only enable this if inline event handlers are essential to your plugin's architecture. See [Manifest Permissions](../../../explanation/concepts/manifest/index.md#permissionsdefinition) for the full list of permission options.

## Choose an event

UXP supports standard HTML events:

| Event Type | Fires When                          | Common Use Cases                     |
| :----------- | :-------------------------------------- | :----------------------------------- |
| `click`    | Element is clicked                  | Buttons, links, interactive elements |
| `input`    | Input value changes                 | Text fields, sliders, checkboxes     |
| `change`   | Input value changes and loses focus | Dropdowns, file inputs               |
| `submit`   | Form is submitted                   | Forms                                |
| `keydown`  | Key is pressed                      | Keyboard shortcuts                   |
| `keyup`    | Key is released                     | Text input validation                |
| `focus`    | Element receives focus              | Input fields                         |
| `blur`     | Element loses focus                 | Input validation                     |

## Listen to Spectrum components

Event listeners work the same way with Spectrum Web Components:

```js
// Spectrum button
const button = document.querySelector("sp-button");
button.addEventListener("click", () => {
  console.log("Spectrum button clicked");
});

// Spectrum slider
const slider = document.querySelector("sp-slider");
slider.addEventListener("input", (event) => {
  console.log(`Slider value: ${event.target.value}`);
});
```
