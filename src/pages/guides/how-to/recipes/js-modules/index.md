---
title: JavaScript Modules
description: Organize UXP plugin code with CommonJS modules, explicit relative imports, and focused responsibilities.
keywords:
  - JavaScript modules
  - require statement
  - module.exports
  - code organization
contributors:
  - https://github.com/karan0207
---

# Organize code with JavaScript modules

<Fragment src="../_shared/prerequisites.md" />

## Use CommonJS modules

Split a plugin into modules when files have distinct responsibilities, such as host operations, validation, and interface helpers. UXP uses CommonJS: export values with `module.exports` and import them with `require()`.

## Export a module

Assign the functions, objects, or values that other files need to `module.exports`:

<CodeBlock slots="heading, code" repeat="1" languages="JavaScript" />

#### utils.js

```js
// Export individual functions
function multiply(a, b) {
    return a * b;
}

function getAnswer() {
    return 42;
}

// Export an object containing all functions
module.exports = {
    multiply,
    getAnswer
};
```

## Import a module

Call `require()` with a relative path from the importing file:

<CodeBlock slots="heading, code" repeat="1" languages="JavaScript" />

#### index.js

```js
// Import the entire module object
const utils = require("./utils.js");
const result = utils.multiply(3, 2); // returns 6

// Or use destructuring to import specific functions
const { multiply, getAnswer } = require("./utils.js");
const answer = getAnswer(); // returns 42
```

## Organize modules in directories

You can organize modules in subdirectories using relative paths:

```text
my-plugin/
├── index.js
├── lib/
│   ├── calculations.js
│   └── helpers.js
└── components/
    └── ui-elements.js
```

<CodeBlock slots="heading, code" repeat="1" languages="JavaScript" />

#### index.js

```js
// Import from subdirectories
const { calculate } = require("./lib/calculations.js");
const { createButton } = require("./components/ui-elements.js");
```

<InlineAlert variant="info" slots="heading, text" />

Use explicit relative paths

UXP does not search global paths for local modules. Include the complete relative path and `.js` extension.

## Complete example

The following command keeps host processing and interface feedback in separate modules:

<CodeBlock slots="heading, code" repeat="3" languages="JavaScript, JavaScript, JavaScript" />

#### index.js

```js
const { entrypoints } = require("uxp");
const { processVideo } = require("./lib/video-processor.js");
const { showNotification } = require("./lib/ui-helpers.js");

entrypoints.setup({
    commands: {
        processCommand: async () => {
            try {
                await processVideo();
                showNotification("Video processing completed!");
            } catch (error) {
                showNotification("Error: " + error.message);
            }
        }
    }
});
```

#### lib/video-processor.js

```js
async function processVideo() {
    // Video processing logic here
    console.log("Processing video...");
    // Simulate async operation
    return new Promise(resolve => setTimeout(resolve, 1000));
}

function getVideoInfo() {
    // Return video information
    return { duration: 120, format: "mp4" };
}

module.exports = {
    processVideo,
    getVideoInfo
};
```

#### lib/ui-helpers.js

```js
function showNotification(message) {
    console.log(`Notification: ${message}`);
    // Additional UI notification logic
}

function createProgressBar() {
    // Progress bar creation logic
    return document.createElement("progress");
}

module.exports = {
    showNotification,
    createProgressBar
};
```
