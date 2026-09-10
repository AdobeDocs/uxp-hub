---
title: Clipboard Operations
description: Integrate your plugin with the system clipboard to read and write text data, so users can copy, paste, and share content between your plugin and other applications.
keywords:
  - clipboard
  - copy
  - paste
  - read write
  - permissions
contributors:
  - https://github.com/karan0207
---

# Use the clipboard

Use `navigator.clipboard` to exchange text with other applications. Request only the access needed for the operation, then handle missing content and permission failures explicitly.

<Fragment src="../_shared/prerequisites.md" />

## Request clipboard access

By default, UXP plugins can't access the system clipboard; this protects users from unwanted data access. If your plugin needs to read or write clipboard content, **you must declare** the right [clipboard permission](../../../explanation/concepts/manifest/index.md#permissionsdefinition) in your `manifest.json`.

Choose the most appropriate permission level:

| Permission       | Access Level          | Use When                                     |
| :----------------- | :----------------------- | :----------------------------------------------- |
| `"read"`         | Read-only             | Your plugin only needs to paste or read data |
| `"readAndWrite"` | Read and write access | Your plugin needs to copy and paste data     |

<InlineAlert variant="info" slots="heading, text"/>

Pick the least-permissive option that meets your needs

In future versions, users may be asked to grant consent for clipboard access, and they'll be more comfortable approving read-only access unless your plugin clearly needs to write data.

## Use the Clipboard API

The clipboard is accessed through `navigator.clipboard`, which provides two main methods:

- **`setContent()`**: Write data to the clipboard.
- **`getContent()`**: Read data from the clipboard.

Both methods work with MIME type objects, where keys represent data formats (like `"text/plain"`) and values contain the actual content.

### Write text

Call `setContent()` with a MIME-keyed object:

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
async function copyToClipboard(text) {
  try {
    await navigator.clipboard.setContent({
      "text/plain": text
    });
    console.log("Text copied to clipboard");
  } catch (error) {
    console.error("Failed to copy to clipboard:", error);
  }
}

// Example usage
copyToClipboard("Welcome to UXP!");
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "clipboard": "readAndWrite"
  }
}
```

### Read text

You can also read content that users have copied from other applications.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
async function pasteFromClipboard() {
  try {
    const clipboardData = await navigator.clipboard.getContent();

    if (clipboardData["text/plain"]) {
      console.log(`Pasted text: ${clipboardData["text/plain"]}`);
      return clipboardData["text/plain"];
    } else {
      console.log("No text data found on clipboard");
      return null;
    }

  } catch (error) {
    console.error("Failed to read from clipboard:", error);
  }
}

// Example usage
pasteFromClipboard();
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "clipboard": "read"
  }
}
```

<InlineAlert variant="info" slots="text"/>

This example requests `"read"` because it never writes to the clipboard. Use the minimum permission level required by the complete workflow.

### Read, transform, and write text

Request `"readAndWrite"` when one operation reads existing content and writes a transformed value back:

```js
// Transform clipboard text to uppercase
async function transformClipboardText() {
  try {
    // Read current clipboard content
    const data = await navigator.clipboard.getContent();

    if (data["text/plain"]) {
      const originalText = data["text/plain"];
      const transformedText = originalText.toUpperCase();

      // Write the transformed text back
      await navigator.clipboard.setContent({
        "text/plain": transformedText
      });

      console.log(`Transformed: "${originalText}" to "${transformedText}"`);
    } else {
      console.log("No text found on clipboard");
    }

  } catch (error) {
    console.error("Failed to transform clipboard text:", error);
  }
}

// Example usage
transformClipboardText();
```

For this use case, your manifest would need `"readAndWrite"` permission.
