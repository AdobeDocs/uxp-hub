---
title: Add Lifecycle Hooks
description: Run setup and teardown logic when a UXP plugin or panel is created, shown, hidden, or destroyed.
keywords:
  - plugin hooks
  - lifecycle
contributors:
  - https://github.com/karan0207
---

# Add lifecycle hooks

Lifecycle hooks let your plugin respond when UXP creates or destroys the plugin container and when users open, show, hide, or close a panel. Use them to initialize state, attach panel content, persist data, and release resources at the appropriate time.

## Choose the right hook

UXP provides hooks at the plugin and panel levels:

| Scope  | Hook        | Runs when                                      |
| :----- | :---------- | :--------------------------------------------- |
| Plugin | `create()`  | The plugin container is initialized.           |
| Plugin | `destroy()` | The plugin container is torn down.              |
| Panel  | `create()`  | The panel is created.                           |
| Panel  | `show()`    | The panel becomes visible.                      |
| Panel  | `hide()`    | The panel is hidden.                            |
| Panel  | `destroy()` | The panel is destroyed.                         |

## Before you begin

<InlineAlert variant="info" slots="text"/>

Panel lifecycle hooks apply only to panel entrypoints. Commands run and exit immediately, so they do not have a persistent panel lifecycle.

<InlineAlert variant="error" slots="heading, text, text2" />

Current limitations

The `hide()` and `destroy()` hooks are not reliable in every host. In Premiere, for example, hiding or closing a panel may not trigger the corresponding callback. Do not depend on these hooks for essential cleanup, and check your target host's release notes for current behavior.

In plugins with multiple panels, lifecycle hooks may fire for all panels without distinguishing which panel triggered the event.

## Implement lifecycle hooks

Declare the panel entrypoint in `manifest.json`, then register its hooks with `entrypoints.setup()`. The key in the `panels` object must match the panel ID in the manifest.

Call `entrypoints.setup()` once and use its two lifecycle properties:

- `plugin` contains the plugin-level `create()` and `destroy()` hooks.
- `panels` contains one property for each panel entrypoint, with that panel's lifecycle hooks as its value.

The following example connects the `firstPanel` manifest entrypoint to its lifecycle handlers:

<CodeBlock slots="heading, code" repeat="2" languages="JSON, JavaScript" />

#### manifest.json

```json
{
  // ...
  "entrypoints": [
    {
      "type": "panel",
      "id": "firstPanel",
      "label": "My plugin",
      "minimumSize": { "width": 400, "height": 400 },
      "maximumSize": { "width": 800, "height": 800 },
      "preferredDockedSize": { "width": 400, "height": 400 },
      "preferredFloatingSize": { "width": 600, "height": 600 }
    }
  ],
  // ...
}
```

#### main.js

```js
const { entrypoints } = require("uxp");

entrypoints.setup({
  plugin: {
    create() {
      console.log("Plugin created");
    },
    async destroy() {
      console.log("Plugin destroyed");
    },
  },
  panels: {
    firstPanel: {
      async create(rootNode) {
        console.log("Panel created", rootNode);
      },
      async show(rootNode, data) {
        console.log("Panel shown", data);
      },
      async hide(rootNode, data) {
        console.log("Panel hidden", data);
      },
      async destroy(rootNode) {
        console.log("Panel destroyed", rootNode);
      },
    },
  },
});
```

## Work with the panel root node

Panel hooks receive a `rootNode` parameter that represents the panel's document root. Use it to attach or remove panel-specific content when a plugin contains [multiple panels](../add-panels/index.md).

The `show()` and `hide()` handlers can also receive `data` passed by the host. In the example, those values are logged so you can inspect when each callback runs.

## Handle asynchronous work

Most panel lifecycle handlers and the plugin `destroy()` handler can return a Promise. Declare a handler with `async` or return a Promise when setup or teardown must finish asynchronous work before the lifecycle transition completes. Keep lifecycle work focused and avoid long-running operations.
