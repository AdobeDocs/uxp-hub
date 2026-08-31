---
title: Linting with ESLint
description: Catch mistakes in your UXP plugin before it loads by setting up ESLint for the UXP environment, in plain JavaScript or TypeScript.
keywords:
  - ESLint
  - Linting
  - Static analysis
  - eslint.config.mjs
  - Flat config
  - typescript-eslint
  - Globals
  - Code quality
contributors:
  - https://github.com/karan0207
---

# Linting with ESLint

[ESLint](https://eslint.org/) reads your source and flags problems before you ever load the plugin: undefined variables, unused code, unreachable branches, and other patterns that tend to become runtime bugs. Add a shared config and it also enforces one style across a team.

It pairs with [TypeScript and IntelliSense](../typescript/index.md): types tell you what an API expects, ESLint tells you when your code does something suspicious with it.

## Why a UXP plugin needs its own config

UXP isn't a browser and isn't Node. Most starter ESLint configs assume one of those two environments, so they get UXP's globals wrong in both directions: `no-undef` flags valid UXP globals like `document` as undefined, while offering you Node or browser globals your plugin can't actually use.

The fix is to tell ESLint what the UXP environment provides, rather than inheriting a `browser` or `node` preset that doesn't match.

## Set up ESLint

Choose between standard JavaScript linting or full type-checked linting with TypeScript:

<CodeBlock slots="heading, code" repeat="2" languages="Standard JS (eslint.config.mjs), Type-Checked TS (eslint.config.mjs)" />

#### Standard JavaScript

```javascript
// @ts-check

import js from "@eslint/js";
import { defineConfig } from "eslint/config";

export default defineConfig(
  { ignores: ["dist/**", "node_modules/**"] },
  js.configs.recommended,
  {
    languageOptions: {
      // UXP loads plugins as CommonJS
      sourceType: "commonjs",
      globals: {
        document: "readonly",
        crypto: "readonly",
      },
    },
  }
);
```

#### Type-Checked TypeScript

```javascript
// @ts-check

import js from "@eslint/js";
import { defineConfig } from "eslint/config";
import tseslint from "typescript-eslint";

export default defineConfig(
  { ignores: ["dist/**", "node_modules/**"] },
  js.configs.recommended,
  tseslint.configs.recommended,
  {
    languageOptions: {
      parserOptions: {
        projectService: true,
      },
    },
  }
);
```

### Installation

For standard JavaScript:

```sh
npm install -D eslint @eslint/js
```

For type-checked TypeScript:

```sh
npm install -D eslint @eslint/js typescript-eslint
```

### Run linting

Add a lint script to your `package.json`:

```json
{
  "scripts": {
    "lint": "eslint ."
  }
}
```

Then run `npm run lint` in your terminal.

## Rules for your host's APIs

Core ESLint doesn't know a host's document model, so it can't catch host-specific misuse. Some hosts publish their own ESLint plugin for exactly that, installed separately.

**Premiere:** [`@adobe/eslint-plugin-premierepro`](https://www.npmjs.com/package/@adobe/eslint-plugin-premierepro) flags action and transaction mistakes that otherwise only surface at runtime. See the [Premiere API reference](https://developer.adobe.com/premiere-pro/uxp/) for host-specific setup, or your host's own reference under **Product API Refs** in the top navigation.

## Fit it into your workflow

- **Editor:** install an ESLint extension so problems show up as you type.
- **Terminal or CI:** run `npm run lint` before you package or open a pull request.
- **Host:** reload the plugin from the UXP Developer Tool once you've fixed the issues (and rebuilt, if you compile).

## Next steps

- [TypeScript and IntelliSense](../typescript/index.md): the type definitions that power typed linting.
- [Debugging your Plugin](../debugging/index.md): for the bugs that only appear once the plugin runs.
- [Package & Distribute](../distribution/overview/index.md): lint before you ship.
