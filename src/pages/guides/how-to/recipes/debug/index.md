---
title: Debugging Techniques
description: Inspect runtime values with console output, temporary dialogs, and the UXP Developer Tool debugger.
keywords:
  - debugging
  - console
  - alerts
  - developer tools
contributors:
  - https://github.com/karan0207
---

# Debugging techniques

Start with console output for quick runtime checks. Use a temporary dialog only when you cannot keep the debugger visible, then move to the UXP Developer Tool for breakpoints and deeper inspection.

<Fragment src="../_shared/prerequisites.md" />

## Log values to the console

Open the UDT debugger and use the **Console** panel to inspect messages, warnings, errors, variables, and objects.

```js
console.log("Plugin initialized");
console.warn("This feature is deprecated");
console.error("Failed to load data");

// Log variables and objects
const user = { name: "Jane", role: "Editor" };
console.log("User data:", user);
// Logs: User data: { name: "Jane", role: "Editor" }

const width = 1920;
const height = 1080;
console.log(`Resolution: ${width}x${height}`);
```

## Show a temporary debug dialog

Use `alert()`, `confirm()`, or `prompt()` for a temporary check when console access is inconvenient. Remove these calls after diagnosing the problem so they do not interrupt the user workflow.

<InlineAlert variant="error" slots="text"/>

Support for `alert()`, `confirm()`, and `prompt()` is limited in Premiere. Prefer console output or the UDT debugger for routine debugging.

<InlineAlert variant="info" slots="heading, text"/>

Enable dialog methods

Set `enableAlerts` in [`manifest.json`](../../../explanation/concepts/manifest/index.md#enablealerts) before calling `alert()`, `confirm()`, or `prompt()`.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
// Simple alert dialog
alert("Plugin loaded successfully");

// Confirmation dialog
const confirmed = confirm("Do you want to continue?");
if (confirmed) {
  console.log("User clicked OK");
} else {
  console.log("User clicked Cancel");
}

// Prompt dialog for user input
const userName = prompt("Enter your name:", "Default Name");
console.log(`User entered: ${userName}`);
```

#### manifest.json

```json
{
  "manifestVersion": 5,
  "featureFlags": {
    "enableAlerts": true
  }
}
```

## Open the full debugger

Use [the UDT debugging workflow](../../udt-deep-dive/plugin-workflows.md#debug-the-plugin) to inspect elements, set breakpoints, step through code, and analyze network requests.
