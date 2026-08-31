---
title: Filesystem Operations
description: Choose a UXP file-system permission and API, then read, write, select, and remember files safely.
keywords:
  - localFileSystem
  - FS API
  - fullAccess
  - file permissions
  - sandbox
contributors:
  - https://github.com/karan0207
---

# Work with files and folders

UXP provides an entry-based `LocalFileSystem` API and a path-based `fs` module. Choose the narrowest permission that supports the task, then select the API based on whether the plugin needs a reusable entry or a direct path operation.

<Fragment src="../_shared/prerequisites.md" />

## Understand the access model

### Sandbox locations

UXP uses a security model that restricts file system access by default. This protected area is called the **sandbox**: a set of safe locations your plugin can access without additional permissions.

**In plugins**, the sandbox includes:

- **Plugin folder**: Your plugin's installation directory (read-only)
- **Data folder**: Persistent storage for your plugin's data
- **Temporary folder**: Transitory storage that may be cleared automatically

<InlineAlert variant="info" slots="text"/>

The temporary folder may be cleared automatically. The data folder persists across application upgrades but is removed when the plugin is uninstalled or its data is cleared. Store irreplaceable user content outside the sandbox.

While the sandbox is sufficient for many use cases, you may need to access other locations, such as the user's Documents folder or a specific project directory. UXP supports this through a **permission-based system**.

### Choose a file-system permission

To access the file system, you must declare the `localFileSystem` permission in your plugin's `manifest.json` file.

<InlineAlert variant="info" slots="heading, text1, text2"/>

Before you proceed

Make sure you understand how [Manifest permissions](../../../explanation/concepts/manifest/index.md#permissionsdefinition) work before implementing file operations in your plugin.

The detailed [`PermissionsDefinition` reference](../../../explanation/concepts/manifest/index.md#permissionsdefinition) explains all available permission options and their security implications.

The `localFileSystem` permission accepts three values:

- **`"plugin"`** (default): provides access only to the sandbox locations. Your plugin will always be able to access the installation directory, data folder, and temporary folder.
- **`"request"`**: allows your plugin to request user permission via file picker dialogs
- **`"fullAccess"`**: grants unrestricted access to the user's file system

Declare the permission in `manifest.json`:

```json
{
  "requiredPermissions": {
    "localFileSystem": "plugin"
  }
}
```

<InlineAlert variant="info" slots="heading, text1, text2"/>

Request the least access

Use `"plugin"` when sandbox storage is sufficient, `"request"` when the user can choose specific files or folders, and `"fullAccess"` only when the plugin must work with fixed arbitrary paths.

### Use file URL schemes

UXP provides convenient URL schemes as shortcuts to specific file system locations:

| Scheme          | Description                                | Permission Required |
| :---------------- | :-------------------------------------------- | :--------------------- |
| `plugin:/`      | Plugin installation folder (read-only)     | `"plugin"`          |
| `plugin-data:/` | Plugin data folder (read-write)            | `"plugin"`          |
| `plugin-temp:/` | Plugin temporary folder (read-write)       | `"plugin"`          |
| `file:/`        | Arbitrary file system location (full path) | `"fullAccess"`      |

You can use these schemes in both HTML and JavaScript:

```html
<img src="plugin:/icons/logo.png" />
<img src="file:/Users/user/Downloads/sample.png" /> <!-- update the path based on your system -->
```

```javascript
const dataFile = await localFileSystem.getEntryWithUrl("plugin-data:/settings.json");
```

## Choose a file API

UXP provides two APIs for file system operations, each suited to different use cases:

| API                 | Best For                             | Access Pattern    |
| :-------------------- | :--------------------------------------- | :-------------------- |
| **LocalFileSystem** | Multiple operations on the same file | Object references |
| **FS Module**       | Single, direct file operations       | Path-based        |

### Use `LocalFileSystem` for entries

The `LocalFileSystem` API is object-oriented and works with **Entry references**: objects that represent files and folders. This approach is ideal when you need to perform multiple operations on the same file or traverse directory structures.

**Access the API:**

```javascript
const { localFileSystem, types } = require('uxp').storage;
```

#### Check entry types

The file system contains two types of items: files and folders. UXP represents these with the `File` and `Folder` classes, both of which extend a base class called `Entry`.

Some methods return an `Entry` because the type can only be determined at runtime. You should check the entry type before performing type-specific operations:

```javascript
const { localFileSystem, types } = require('uxp').storage;

async function createFileInFolder() {
    try {
        // Create a folder entry
        const folderEntry = await localFileSystem.createEntryWithUrl(
            "plugin-temp:/myFolder",
            { type: types.folder }
        );

        // Verify it's a folder before using folder-specific methods
        if (folderEntry.isFolder) {
            const newFile = await folderEntry.createFile("data.txt", { overwrite: true });
            await newFile.write("This is sample content.");
            console.log(`File created at: ${newFile.nativePath}`);
        }
    } catch (e) {
        console.error("Failed to create file:", e);
    }
}
```

### Use `fs` for direct operations

The `fs` module provides a path-based API similar to [Node.js file system operations](https://nodejs.org/api/fs.html). It's ideal for straightforward, single-operation tasks like reading a configuration file or writing output.

**Access the API:**

```javascript
const fs = require("fs");
```

## Work with `LocalFileSystem`

### Access sandbox storage

When your plugin only needs to work with files in its own sandbox, use the `"plugin"` permission level, or leave the `localFileSystem` field empty, which defaults to `"plugin"` anyway.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
const { localFileSystem } = require('uxp').storage;

async function accessPluginDataFolder() {
    // Access the plugin's data folder
    try {
        const dataFolder = await localFileSystem.getEntryWithUrl("plugin-data:/");
        console.log(`Data folder path: ${dataFolder.nativePath}`);

        // List all files in the data folder
        const entries = await dataFolder.getEntries();
        console.log(`Found ${entries.length} items in data folder`);

        for (const entry of entries) {
            console.log(`- ${entry.name} (${entry.isFile ? 'file' : 'folder'})`);
        }
    } catch (e) {
        console.error("Failed to access data folder:", e);
    }
}
```

#### manifest.json

```json
{
  "manifestVersion": 5,
  "requiredPermissions": {
    "localFileSystem": "plugin"
  }
}
```

### Access a fixed absolute path

When your plugin needs unrestricted access to the file system, use the `"fullAccess"` permission level. This requires you to specify absolute file paths using the `file:/` scheme.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
const { localFileSystem } = require('uxp').storage;

async function accessUserDocuments() {
  // Access a specific location outside the sandbox
  try {
    // For macOS
    const documentsFolder = await localFileSystem.getEntryWithUrl(
    "file:/Users/user/Documents" // Replace with a valid path.
    );
    // For Windows, use: "file:/C:/Users/user/Documents"

    console.log(`Documents folder path: ${documentsFolder.nativePath}`);

    // Read a specific file
    const configFile = await localFileSystem.getEntryWithUrl(
    "file:/Users/user/Documents/config.json" // Replace with a valid path.
    );
    if (configFile.isFile) {
      const content = await configFile.read();
      console.log("Config file content:", content);
    }
  } catch (e) {
    console.error("Failed to access documents folder:", e);
  }
}
```

#### manifest.json

```json
{
  "manifestVersion": 5,
  "requiredPermissions": {
    "localFileSystem": "fullAccess"
  }
}
```

<InlineAlert variant="warning" slots="text"/>

Use `"fullAccess"` only when a user-selected entry cannot support the workflow. Prefer `"request"` when users can choose the required location themselves.

### Ask the user to choose a location

The most user-friendly approach is to let users choose which files or folders your plugin can access. Use the `"request"` permission level with file picker dialogs.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
const { localFileSystem, domains, fileTypes } = require('uxp').storage;

async function openUserSelectedFile() {
    // Present a file picker starting at the user's Desktop
    try {
        const file = await localFileSystem.getFileForOpening({
            initialDomain: domains.userDesktop,
            types: fileTypes.text
        });

        if (!file) {
            console.log("User cancelled file selection");
            return;
        }

        // Read the selected file
        const content = await file.read();
        console.log(`File content:\n${content}`);
        console.log(`File path: ${file.nativePath}`);
    } catch (err) {
        console.error("Failed to open file:", err);
    }
}

async function saveToUserSelectedLocation() {
    // Present a save dialog to let the user choose where to save
    try {
        const file = await localFileSystem.getFileForSaving("export.txt", {
            types: ["txt"]
        });

        if (!file) {
            console.log("User cancelled save operation");
            return;
        }

        // Write content to the selected location
        await file.write("This content was exported from the plugin.");
        console.log(`File saved to: ${file.nativePath}`);
    } catch (err) {
        console.error("Failed to save file:", err);
    }
}

async function selectFolderForExport() {
    // Let the user select a folder for batch export
    try {
        const folder = await localFileSystem.getFolder({
            initialDomain: domains.userDocuments
        });

        if (!folder) {
            console.log("User cancelled folder selection");
            return;
        }

        // Create multiple files in the selected folder
        for (let i = 1; i <= 3; i++) {
            const file = await folder.createFile(`export_${i}.txt`, { overwrite: true });
            await file.write(`Content for file ${i}`);
        }

        console.log(`Created 3 files in: ${folder.nativePath}`);
    } catch (err) {
        console.error("Failed to export files:", err);
    }
}
```

#### manifest.json

```json
{
  "manifestVersion": 5,
  "requiredPermissions": {
    "localFileSystem": "request"
  }
}
```

<InlineAlert variant="info" slots="heading,text"/>

Domain tokens

In the example above, we use the `domains.userDesktop` token to get the user's Desktop folder. You can use other domain tokens, such as `domains.userDocuments`, `domains.userDownloads`, and more.

### Remember a selection with tokens

When users grant access to files or folders, you can create tokens to remember those locations across sessions, saving users from repeatedly selecting the same files.

UXP provides two types of tokens:

- **Session tokens**: Last until the plugin is unloaded or the application closes
- **Persistent tokens**: Survive across sessions until the plugin is uninstalled

```javascript
const { localFileSystem, domains, fileTypes } = require('uxp').storage;

async function selectAndRememberFile() {
    try {
        // Let the user select a file
        const file = await localFileSystem.getFileForOpening({
            initialDomain: domains.userDesktop,
            types: fileTypes.text
        });

        if (!file) {
            console.log("User cancelled file selection");
            return;
        }

        // Create a persistent token for this file
        const token = await localFileSystem.createPersistentToken(file);

        // Store the token for future use (e.g., in localStorage)
        localStorage.setItem("selectedFileToken", token);

        console.log(`File selected and token saved: ${file.nativePath}`);
    } catch (err) {
        console.error("Failed to create token:", err);
    }
}

async function readPreviouslySelectedFile() {
    try {
        // Retrieve the token from storage
        const token = localStorage.getItem("selectedFileToken");

        if (!token) {
            console.log("No previously selected file found");
            return;
        }

        // Access the file using the token
        const file = await localFileSystem.getEntryForPersistentToken(token);

        // Read the file content
        const content = await file.read();
        console.log(`File content:\n${content}`);
    } catch (err) {
        console.error("Failed to read file using token:", err);
        // Token may be invalid if file was deleted or moved
        localStorage.removeItem("selectedFileToken");
    }
}
```

<InlineAlert variant="info" slots="heading, text"/>

Store tokens safely

Store persistent tokens in `localStorage` or the plugin data folder. Handle invalid tokens when a user moves or deletes the referenced entry.

## Work with the `fs` module

The `fs` module offers a simpler, path-based approach similar to [Node.js](https://nodejs.org/api/fs.html). It's ideal for quick, single-operation tasks.

### Read and write sandbox files

Read a file directly from the sandbox using a URL scheme:

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
const fs = require("fs");

async function readConfigFile() {
    // Read a configuration file from the plugin folder
    try {
        const content = await fs.readFile("plugin:/config.json", "utf8");
        const config = JSON.parse(content);
        console.log("Configuration loaded:", config);
    } catch (e) {
        console.error("Failed to read config file:", e);
    }
}

async function writeToDataFolder() {
    // Write data to the plugin's data folder
    try {
        const data = {
            lastRun: new Date().toISOString(),
            version: "1.0.0"
        };

        await fs.writeFile(
            "plugin-data:/state.json",
            JSON.stringify(data, null, 2),
            "utf-8"
        );

        console.log("State saved successfully");
    } catch (e) {
        console.error("Failed to save state:", e);
    }
}
```

#### manifest.json

```json
{
  "manifestVersion": 5,
  "requiredPermissions": {
    "localFileSystem": "plugin"
  }
}
```

<InlineAlert variant="info" slots="text"/>

The `plugin:/`, `plugin-data:/`, and `plugin-temp:/` URL schemes work with both `fs` and `LocalFileSystem`.

### Read and write absolute paths

Write files to any location on the file system using absolute paths:

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
const fs = require("fs");

async function exportToDesktop() {
    // Export data to the user's Desktop
    try {
        const exportData = "Exported data from plugin";

        // For macOS
        await fs.writeFile(
            "/Users/user/Desktop/export.txt", // Replace with a valid path.
            exportData,
            { encoding: "utf-8" }
        );
        // For Windows, use: "C:/Users/user/Desktop/export.txt"

        console.log("File exported to Desktop");
    } catch (e) {
        console.error("Failed to export file:", e);
    }
}

async function readFromDesktop() {
    // Read a file from the user's Desktop folder
    try {
        // For macOS
        const content = await fs.readFile(
            "/Users/user/Desktop/export.txt", // Replace with a valid path.
            "utf8"
        );
        // For Windows, use: "C:/Users/user/Desktop/export.txt"

        console.log("File content:", content);
    } catch (e) {
        console.error("Failed to read file:", e);
    }
}
```

#### manifest.json

```json
{
  "manifestVersion": 5,
  "requiredPermissions": {
    "localFileSystem": "fullAccess"
  }
}
```

## Handle file-system constraints

### Account for operating-system restrictions

Even with `"fullAccess"` permission, certain system locations may be restricted by the operating system:

- **macOS and Windows**: Generally allow access to most user-accessible locations
- **UWP (Windows Store apps)**: System folders are prohibited and cannot be accessed

<InlineAlert variant="info" slots="text"/>

Always handle file system errors gracefully and inform users when access is denied. Operating system security policies may prevent access to certain locations even when your plugin has the correct permissions.

### Follow file-system best practices

1. **Choose the right permission level**: start with `"plugin"`, upgrade to `"request"`, and only use `"fullAccess"` when absolutely necessary.
2. **Handle errors gracefully**: always wrap file operations in try-catch blocks.
3. **Validate file paths**: check that files exist before attempting to read them.
4. **Use appropriate encoding**: specify `"utf-8"` for text files to ensure proper character handling.
5. **Provide user feedback**: show progress indicators for long-running file operations.
6. **Clean up temporary files**: delete temporary files when they're no longer needed.

### Choose an API by task

| Use Case                             | Recommended API |
| :--------------------------------------- | :------------------ |
| Multiple operations on the same file | LocalFileSystem |
| Traversing directory structures      | LocalFileSystem |
| Working with file metadata           | LocalFileSystem |
| Creating persistent tokens           | LocalFileSystem |
| Quick read/write operations          | FS Module       |
| Simple path-based operations         | FS Module       |
| Familiarity with Node.js fs API      | FS Module       |
