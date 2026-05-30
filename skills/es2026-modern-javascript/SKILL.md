---
name: es2026-modern-javascript
description: Use when writing, reviewing, or configuring JavaScript/TypeScript that uses ES2024, ES2025, ES2026, modern browser targets, Vite, Babel, core-js, browserslist, caniuse checks, polyfills, or proposal-era APIs.
---

# ES2026 Modern JavaScript

## Overview

Use modern JavaScript deliberately: target 2024+ evergreen browsers, avoid ES5/ES6 legacy output, and verify each ES2025/ES2026 feature by exact API rather than by edition year. Vite/Babel targets handle syntax transforms; runtime APIs still need native support, `core-js`, a ponyfill, or a feature gate.

**Required companion for dates:** Use `temporal-js` for Temporal details and the `temporal-polyfill` convention.

## Baseline Policy

Default browser floor for this skill:

```txt
Chrome >= 121
Edge >= 121
Firefox >= 122
Safari >= 17.4
iOS >= 17.4
```

Do not use `defaults` or `not dead` as the only policy when the task says modern JavaScript. They are market queries, not a product support contract.

## Verification Rule

Before approving a modern feature, check all four:

1. **Edition/status:** TC39 finished proposals or current ECMA-262 draft.
2. **Browser support:** caniuse plus MDN browser compatibility for the exact API/syntax.
3. **Toolchain:** Vite/Oxc/esbuild parse/transform support, TypeScript parser/types, Babel plugins if used.
4. **Runtime polyfill:** `core-js`, a focused ponyfill, or feature detection. Syntax cannot be polyfilled at runtime.

If any answer is unknown, do not claim the feature is safe. Add a targeted compatibility check or avoid the feature.

## Vite Defaults

Vite 8 defaults to Baseline Widely Available (`chrome111`, `edge111`, `firefox114`, `safari16.4` as of 2026-01-01), which is older than this skill's preferred 2024+ floor. Set explicit targets when the project policy is stricter:

```ts
// vite.config.ts
import { defineConfig } from 'vite';

export default defineConfig({
  build: {
    target: ['chrome121', 'edge121', 'firefox122', 'safari17.4']
  }
});
```

Vite transforms syntax but does **not** polyfill runtime APIs. Add polyfills only for APIs actually used and missing in the target matrix.

## Browserslist

```txt
Chrome >= 121
Edge >= 121
Firefox >= 122
Safari >= 17.4
iOS >= 17.4
```

Use browserslist for Babel, Autoprefixer, eslint-plugin-compat, and docs. Do not assume Vite's JS build target reads browserslist automatically; configure `build.target` explicitly.

## Babel/Core-JS Pattern

Prefer no Babel for normal Vite apps unless the project already uses it or a specific syntax transform is needed. When Babel is used for API polyfills:

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "bugfixes": true,
        "useBuiltIns": "usage",
        "corejs": "3.49"
      }
    ]
  ]
}
```

Rules:

- Pin the installed `core-js` minor version; `corejs: 3` may miss newer modules.
- Do not combine `useBuiltIns: "usage"` with a broad global `import "core-js/actual"` unless intentionally audited.
- For reusable libraries, avoid global mutation; prefer documented peer requirements, feature gates, or `core-js-pure`/ponyfills.
- For proposal/candidate APIs, `@babel/preset-env` may not inject them unless configured with proposal support or explicit imports.

## Edition Summary

Load [references/es2024-es2026-reference.md](references/es2024-es2026-reference.md) for per-feature examples, support notes, and polyfill modules.

| Edition | Normal stance for 2024+ apps |
| --- | --- |
| ES2024 | Mostly usable; verify ArrayBuffer/SharedArrayBuffer/Atomics details and `/v` parser support. |
| ES2025 | Use selectively with caniuse/MDN checks; often needs `core-js` or syntax/tooling support. |
| ES2026 | Use as finished proposal set, but verify native support; many need `core-js` or fallbacks. |
| Temporal / Explicit Resource Management | Current TC39 data lists these for 2027, not ES2026. Treat as opt-in features, not ES2026 baseline. |

## Quick Polyfill Index

```ts
// Targeted examples only. Import at app entry before feature use.
import 'core-js/actual/object/group-by';
import 'core-js/actual/map/group-by';
import 'core-js/actual/promise/with-resolvers';
import 'core-js/actual/iterator';
import 'core-js/actual/set';
import 'core-js/actual/promise/try';
import 'core-js/actual/regexp/escape';
import 'core-js/actual/array/from-async';
```

Temporal:

```ts
import { Temporal } from 'temporal-polyfill';
```

Syntax/plugin examples:

```bash
npm install --save-dev @babel/core @babel/preset-env
npm install core-js temporal-polyfill
```

Use Babel plugins only when needed by the target matrix, such as Unicode set regex transforms, RegExp modifiers, duplicate named capture groups, import attributes parsing, or explicit resource management syntax.

## Common Mistakes

| Mistake | Correction |
| --- | --- |
| `build.target: "es2018"` for 2024+ browsers | Use explicit modern browser targets or a modern ES target. |
| `browserslist: defaults` as product policy | Write concrete Chrome/Firefox/Safari/iOS floors. |
| Assuming Vite target polyfills APIs | Vite transforms syntax; runtime APIs need native support/polyfills. |
| Combining `useBuiltIns: "usage"` with global `core-js/actual` | Pick one audited polyfill strategy. |
| Calling ES2026 candidate APIs safe because Chrome ships them | Check TC39 status plus Firefox/Safari/caniuse/MDN. |
| Treating syntax like `/v`, import attributes, or `using` as runtime-polyfillable | Configure parser/transform support or avoid. |
| Recommending `@js-temporal/polyfill` by habit | Use `temporal-js`; default to `temporal-polyfill` in this environment. |
| Listing Temporal or `using` as finalized ES2026 | Current TC39 finished proposals list them as 2027; treat as opt-in. |
