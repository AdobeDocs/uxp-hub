# UXP Hub: Developer Documentation

**UXP Hub** is the central entry point for UXP development, bringing
together the UXP API reference, the CEP to UXP migration center,
per-application version support, and community resources across every
UXP-enabled host application.

The site is built on Adobe's EDS / devsite platform. Pages are authored as
Markdown under [`src/pages/`](src/pages/) and rendered under the
`/uxp/` path on the [Adobe Developer site](https://developer.adobe.com/).

## Repository layout

| Path | Contents |
| --- | --- |
| `src/pages/` | All documentation pages, one folder per page with an `index.md`. |
| `src/pages/config.md` | Site navigation (sidebar, top nav) and the URL path prefix. |
| `src/pages/uxp-api/` | The UXP platform API reference (JavaScript, CSS, HTML, Spectrum), plus the changelog and version details pages. |
| `src/pages/migration-center/` | CEP to UXP migration guides. |
| `src/pages/images/` | Screenshots and other page assets. |
| `package.json` | Authoring and lint scripts (see below). |

## Develop locally

Local preview needs three servers running at the same time: this content repo,
the EDS code server, and the runtime connector. More detail lives in the
[`adp-devsite-utils`](https://github.com/AdobeDocs/adp-devsite-utils) repo.

1. Clone, install, and run this **content** repo:

   ```sh
   git clone https://github.com/AdobeDocs/uxp-hub
   cd uxp-hub
   npm install
   npm run dev
   ```

2. Clone, install, and run the **code** server ([adp-devsite](https://github.com/AdobeDocs/adp-devsite)):

   ```sh
   git clone https://github.com/AdobeDocs/adp-devsite
   cd adp-devsite
   npm install
   npm run dev
   ```

3. Clone, install, and run the **runtime connector** ([devsite-runtime-connector](https://github.com/aemsites/devsite-runtime-connector)):

   ```sh
   git clone https://github.com/aemsites/devsite-runtime-connector
   cd devsite-runtime-connector
   npm install
   npm run dev
   ```

Then open [http://localhost:3000/uxp/](http://localhost:3000/uxp/).
Use the exact path prefix, including the trailing slash.

## Make changes

- **Add or edit a page:** create or edit an `index.md` inside a folder under
  `src/pages/`. Each page needs YAML frontmatter (`title`, `description`,
  `keywords`).
- **Change navigation:** edit `src/pages/config.md`. After changing it, stop the
  content server, restart it, and reload the page in a new browser tab.
- **Link and image paths** are relative to the page's folder.

### Markdown rules

EDS is stricter than local Markdown preview, so a page can look fine locally and
break once deployed. The two that bite most often:

- **No em dashes (`—`) or en dashes (`–`).** They render locally but turn into
  garbage characters on deploy. Use commas, parentheses, or colons, and a single
  hyphen (`-`) only for compound modifiers and ranges.
- **Comment syntax is `{/* ... */}`**, not `<!-- ... -->`.

## Scripts

Run these from the repo root with `npm run <script>`:

| Script | What it does |
| --- | --- |
| `dev` | Start the local content server. |
| `lint` | Run the docs linter (Markdown rules, links), verbose. |
| `lint:errorOnly` | Run the docs linter, errors only. |
| `link:checkAllLinks` | Check internal and external links. |
| `buildNavigation` | Regenerate navigation from `config.md`. |
| `buildContributors` | Regenerate the contributors list. |
| `buildRedirections` | Regenerate redirects. |

See [`package.json`](package.json) for the full list, including
`normalizeLinks`, `renameFiles`, and the staging/production redirect
checkers.

## Maintainers

This documentation is maintained by **UXP DevRels**. For questions or issues,
open an issue in this repository.
