---
title: TypeScript and IntelliSense
description: Add autocomplete, inline documentation, and type checking to your UXP plugin using the UXP type definitions, in plain JavaScript or full TypeScript.
keywords:
  - TypeScript
  - IntelliSense
  - Autocomplete
  - Type checking
  - jsconfig.json
  - tsconfig.json
  - JSDoc
  - Type definitions
  - "@adobe/cc-ext-uxp-types"
contributors:
  - https://github.com/karan0207
---

# TypeScript and IntelliSense

UXP type definitions provide autocomplete, inline API documentation, and editor diagnostics before you load the plugin. You can use them with plain JavaScript or a compiled TypeScript project.

You don't need TypeScript to benefit here. Point your editor at the type definitions and a plain JavaScript project gets the same autocomplete and type hints, with no build step. Reach for TypeScript when you want compile-time checking and stricter refactoring on a larger codebase.

## Install the UXP type definitions

The [`@adobe/cc-ext-uxp-types`](https://github.com/adobe/cc-ext-uxp-types) package covers the UXP APIs shared across every UXP host: the `uxp` module, Node-style modules like `fs` and `os`, the Spectrum UXP widgets, and UXP's own DOM.

```sh
npm install --save-dev @adobe/cc-ext-uxp-types

# Using yarn
yarn add -D @adobe/cc-ext-uxp-types

# Using pnpm
pnpm add -D @adobe/cc-ext-uxp-types
```

These types stop at the UXP platform. Your host application's own APIs (its documents, selections, and commands) ship as a separate package. See [Your host's own APIs](#your-hosts-own-apis) below.

## Configure your editor

Add a config file to your plugin's root so the editor knows where the UXP types live. Use `jsconfig.json` for a JavaScript project or `tsconfig.json` for TypeScript.

<CodeBlock slots="heading, code" repeat="2" languages="jsconfig.json, tsconfig.json" />

#### JavaScript (jsconfig.json)

```json
{
  "compilerOptions": {
    "checkJs": true,
    "target": "ES2020",
    "lib": ["ES2020"],
    "typeRoots": [
      "node_modules/@types",
      "node_modules/@adobe/cc-ext-uxp-types"
    ]
  },
  "include": ["*.js"],
  "exclude": ["node_modules", "dist"]
}
```

#### TypeScript (tsconfig.json)

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "typeRoots": [
      "node_modules/@types",
      "node_modules/@adobe/cc-ext-uxp-types"
    ]
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

Two critical configuration details:

- `typeRoots` must list `node_modules/@types` explicitly. Setting `typeRoots` overrides defaults, so omitting it hides other `@types` packages.
- `lib` deliberately omits `"DOM"`. UXP provides its own `Document`, `HTMLElement`, and DOM types. Adding the browser `"DOM"` library creates conflicting global definitions.

Restart your editor after adding either config file so it loads the definitions.

## Use JavaScript with JSDoc

This approach adds type checking without introducing a build step. Your existing `index.js` or `main.js` workflow remains unchanged.

Create a `jsconfig.json`:

```json
{
  "compilerOptions": {
    "checkJs": true,
    "target": "ES2020",
    "lib": ["ES2020"],
    "typeRoots": ["node_modules/@types", "node_modules/@adobe/cc-ext-uxp-types"]
  },
  "include": ["*.js"],
  "exclude": ["node_modules", "dist"]
}
```

With that in place, the UXP APIs autocomplete on their own. No per-file annotation needed:

```javascript
const uxp = require("uxp");
const fs = require("fs");

// host, versions, and the rest autocomplete from here
const entry = await fs.getFileForOpening();
console.log(uxp.host.name);
```

Use [JSDoc](https://jsdoc.app/) comments where you want to type your own function parameters, so the values flowing through them keep their hints:

```javascript
/**
 * @param {string} name
 * @returns {Promise<void>}
 */
async function greet(name) {
  const dialog = document.querySelector("dialog");
  dialog.querySelector("h1").textContent = `Hello, ${name}`;
  await dialog.uxpShowModal();
}
```

`checkJs` surfaces type mistakes as editor warnings rather than build errors, so nothing blocks you from loading the plugin. That's the tradeoff: instant feedback, no compile step, but the checks are advisory. If the project grows, move to TypeScript.

## Use TypeScript with a build step

TypeScript adds compile-time checking and the full type system, at the cost of a build. Your plugin still loads plain JavaScript, so you compile `.ts` source down to a `dist/` folder and point the manifest's HTML at the output.

1. Install TypeScript and create a source folder:

   ```sh
   npm install --save-dev typescript
   mkdir src
   ```

2. Add a `tsconfig.json`:

   ```json
   {
     "compilerOptions": {
       "target": "ES2020",
       "module": "commonjs",
       "lib": ["ES2020"],
       "outDir": "./dist",
       "rootDir": "./src",
       "strict": true,
       "esModuleInterop": true,
       "skipLibCheck": true,
       "typeRoots": ["node_modules/@types", "node_modules/@adobe/cc-ext-uxp-types"]
     },
     "include": ["src/**/*"],
     "exclude": ["node_modules", "dist"]
   }
   ```

3. Move your code into `src/` as `.ts` files. Your project ends up like this:

   ```text
   your-plugin/
   ├── tsconfig.json
   ├── package.json
   ├── manifest.json
   ├── index.html
   ├── src/
   │   └── main.ts        # your TypeScript source
   └── dist/
       └── main.js        # compiled output
   ```

4. Add build scripts to `package.json`:

   ```json
   {
     "scripts": {
       "build": "tsc",
       "watch": "tsc --watch"
     }
   }
   ```

5. Point `index.html` at the compiled output, not the source:

   ```html
   <script src="dist/main.js"></script>
   ```

6. Build:

   ```sh
   npm run build
   ```

You now get full inference and strict checking as you write:

```typescript
const uxp = require("uxp");

async function logHost(): Promise<void> {
  const name: string = uxp.host.name;
  console.log(`Running in ${name}`);
}
```

## Reload compiled output in UDT

The UXP Developer Tool watches source files, not your build step. Since `tsc` writes to `dist/`, run the compiler in watch mode and tell UDT to load from the distribution folder so rebuilds reload automatically:

- Run `npm run watch` in a terminal.
- In UDT, use **Load & Watch**, and set the distribution folder under the plugin's **••• > Options... > Advanced**.

The [Working with Bundlers](../udt-deep-dive/plugin-workflows.md#working-with-bundlers) section covers this source-versus-distribution setup in full, and it applies to any build tool, not just `tsc`.

## Install host API types

`@adobe/cc-ext-uxp-types` types the UXP platform, not a host's document model. Each host publishes its own type-definitions package, installed alongside the UXP types, and documents it on its own site.

**Premiere:** the [`@adobe/premierepro`](https://www.npmjs.com/package/@adobe/premierepro) package types `Project`, `Sequence`, and the rest. See the [Premiere API reference](https://developer.adobe.com/premiere-pro/uxp/) for host-specific setup, or your host's own reference under **Product API Refs** in the top navigation.

## Known limitations

The UXP types are generated from a JavaScript codebase, so a few gaps are expected:

- Some parameters come through as `any` where the source doesn't specify a type.
- A handful of supported globals, such as `window` and `navigator`, aren't typed yet.
- `document.createElement("img")` returns a generic element, so it won't suggest image-specific members. Construct the element (`new HTMLImageElement()`) when you want those hints.

## Next steps

- [Understanding UXP APIs](../../explanation/fundamentals/apis/index.md): which APIs you're now getting hints for, and when to use each.
- [Debugging your Plugin](../debugging/index.md): catch the errors types can't, once the plugin is running.
