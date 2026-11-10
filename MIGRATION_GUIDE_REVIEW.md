# Migration guide update: maintainer review

Source branch: `staging-branch` at `22db7c39186ed906265f72f3b69490a80d748e1b`. Documentation review date: November 4, 2026.

This file records unresolved owner decisions and runtime checks for the migration-guide fixes. It is not a claim that the examples have passed installed-host tests.

## Scope

Only three migration-center pages are modified. The cross-host differences page, all existing UXP API pages, and the shared manifest reference have been restored to match `origin/staging-branch`. The product roadmap section remains unchanged from staging.

## Applied corrections

- Corrected Photoshop `host.data.apiVersion` and defaults based on `host.minVersion`; separated manifest, UXP runtime, and host API versions.
- Documented manifest v5 filesystem permissions, entry-based and path-based APIs, least privilege, and persistent-token recovery.
- Clarified entrypoint signatures, optional Promise handling, method-specific timeout guidance, host visibility limitations, and reserved command cancellation.
- Separated flyout labels from manifest entrypoint labels in the migration guide.
- Added Photoshop modal mutation, InDesign module/script-lifetime, and Premiere action-locking guidance.
- Added UI/dependency preflight and current documented Hybrid Plugin host/version restrictions.
- Made the migration guide's added/updated JSON fragments parseable and documented shell error results.
- Preserved the existing product roadmap section, image, per-host milestones, and date wording unchanged from staging, as requested by the content owner.

## Owner decisions still required

### WebView version boundaries

The [9.1 changelog](src/pages/uxp-api/changelog.md#uxp-v91) lists removal of `webview.allow`, gesture changes, and more detailed load errors; the existing API reference attributes some of these to 9.2. Those references remain unchanged, as requested. This historical discrepancy is not resolved by the migration-guide update.

Ask the runtime owner to establish the exact introduction/removal releases, omitted-domain behavior, and corresponding host versions. Historical release notes have not been silently reassigned to a different version.

### Selector availability

The existing `:first-child` reference says “Since UXP 3.0,” while [UXP 8.1 release notes](src/pages/uxp-api/changelog.md#uxp-v810) list new selector support. Both references remain unchanged. Confirm whether the later change concerns Web Components/shadow DOM or a different implementation context before proposing a separate API-reference change.

### Historical shell and IPC floors

The migration guide previously claimed an exact UXP 6.0.2 floor. The current references establish the APIs and manifest permissions, but not that exact first-supported patch. The guide now asks readers to verify their minimum host/runtime instead of publishing an unverified historical floor. Restore an exact gate only with a release source and host mapping.

### Lifecycle and framework support

Re-test historical Photoshop issue PS-57284 before assigning fixed versions to panel show/hide callbacks. Do not promise `commands.cancel` support without a documented host implementation. Establish a current tested React/SWC/bundler matrix instead of extrapolating from UXP 6.4 framework notes.

## Documentation validation

- Whitespace validation: `git diff --check` passed.
- Relative Markdown link targets in the changed/added documents resolved to existing files, including the restored roadmap image.
- Checked migration-page relative links, heading fragments, and new/modified JSON and JavaScript syntax after restoring the API references. These are syntax checks, not Adobe-host execution tests.
- Earlier scoped Markdown checks reported no new diagnostics against staging. The whole-site network linter did not complete and was stopped; a full external-link sweep is not claimed as passing.
- A separate read-only review found no additional actionable factual/API issues.

## Installed-host verification checklist

No installed-host execution has been performed as part of this documentation update. Record host build, UXP runtime, OS, manifest version, and library/toolchain versions for each test.

- [ ] Filesystem `plugin`, `request`, and `fullAccess` behavior; OS denial; missing/moved files; token recovery.
- [ ] Plugin/panel load and teardown; repeated show/hide; Promise completion and timeout behavior.
- [ ] Photoshop API v2 state changes inside modal scope; error/cancellation paths; minimum supported host.
- [ ] Premiere 26.3+ action creation inside locks and synchronous transaction callbacks.
- [ ] InDesign 18.4+ imports, DOM-version selection, and script completion with global await.
- [ ] IPC with installed, missing, disabled, or rejecting responders; no cross-host behavior assumed.
- [ ] WebView remote/local rendering, explicit domains, bridge source/origin checks, subframes, and drag/drop.
- [ ] UI libraries, keyboard navigation, dialogs, password inputs, and shell consent/error handling on macOS and Windows.

The documentation changes can address factual and example defects without these tests, but runtime conformance and an exact feature-introduction matrix remain separate release-review tasks. The existing product roadmap content is outside the technical corrections and remains unchanged.