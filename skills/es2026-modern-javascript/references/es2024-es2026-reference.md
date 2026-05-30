# ES2024-ES2026 Reference

## Source Map

Primary sources to check before detailed guidance:

- TC39 finished proposals: https://github.com/tc39/proposals/blob/HEAD/finished-proposals.md
- ECMAScript current draft: https://tc39.es/ecma262/
- ECMAScript 2025 PDF: https://ecma-international.org/publications-and-standards/standards/ecma-262/
- MDN JavaScript reference: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference
- caniuse: https://caniuse.com/
- Vite build target docs: https://vite.dev/config/build-options.html#build-target
- Vite browser compatibility: https://vite.dev/guide/build.html#browser-compatibility
- Babel preset-env: https://babel.dev/docs/babel-preset-env
- core-js docs: https://core-js.io/
- core-js repository/modules: https://github.com/zloirock/core-js
- Local report used when creating this skill: `/home/inky/Загрузки/es2024_es2026_development_polyfills.html`
- Temporal skill: `/home/inky/.agents/skills/temporal-js/SKILL.md`

Always check caniuse/MDN for exact feature names and exact browser versions. Browser support can change faster than skill docs.

## ES2024

Finished proposals expected/publication year 2024 in TC39 data:

| Feature | Use for | Compatibility/polyfill |
| --- | --- | --- |
| `Object.groupBy`, `Map.groupBy` | Group iterable items without reduce boilerplate. `Object.groupBy` uses property keys and returns a null-prototype object; `Map.groupBy` preserves key identity. | Usually safe under Chrome 121+/Safari 17.4+; verify Firefox and WebViews. `core-js/actual/object/group-by`, `core-js/actual/map/group-by`. |
| `Promise.withResolvers` | Promise plus external `resolve`/`reject` hooks for queues, deferred UI, event bridges. | Verify Safari/WebViews. `core-js/actual/promise/with-resolvers`. Avoid long-lived resolver leaks. |
| ArrayBuffer transfer | `buffer.transfer()`, `transferToFixedLength()` for zero-copy ownership transfer/detach patterns. | Memory semantics cannot be fully simulated in old engines. Feature-detect. core-js has modules, but prefer fallback code for critical paths. |
| Resizable `ArrayBuffer`, growable `SharedArrayBuffer` | Dynamic binary buffers and shared memory growth. | Feature-detect; Firefox support lagged through part of 2024. Polyfills cannot supply identical native memory semantics. |
| RegExp `/v` flag and set notation | Unicode set operations, properties of strings, intersections/subtractions. | Syntax/engine feature. Unsupported browsers fail at parse time. Use Babel regex transforms or avoid literal syntax in unsupported matrices. |
| `Atomics.waitAsync` | Non-blocking waits on shared memory. | Feature-detect. Requires shared memory prerequisites; not a normal app-level polyfill. |
| `String.prototype.isWellFormed`, `toWellFormed` | Detect/fix lone surrogates before encoding, URLs, logs, and UTF-8 boundaries. | `core-js/actual/string/is-well-formed`, `core-js/actual/string/to-well-formed`. |

Example:

```ts
const byKind = Object.groupBy(items, item => item.kind);
const byOwner = Map.groupBy(items, item => item.owner);

const { promise, resolve, reject } = Promise.withResolvers<Response>();

const safeLabel = label.toWellFormed();
```

## ES2025

Finished proposals expected/publication year 2025 in TC39 data:

| Feature | Use for | Compatibility/polyfill |
| --- | --- | --- |
| `RegExp.escape` | Safely insert user strings into generated regular expressions. | `core-js/actual/regexp/escape` or `regexp.escape`. Prefer this over hand-written escaping. |
| Redeclarable global `eval` vars | Normative semantics cleanup. | Usually no direct app action. |
| Float16 | `Float16Array`, `DataView#getFloat16/setFloat16`, `Math.f16round` for ML/graphics/binary memory savings. | Verify browser support. `core-js/actual/float16-array`, related DataView/Math modules if available. Precision is limited. |
| `Promise.try` | Normalize maybe-sync/maybe-async work into promise chains. | `core-js/actual/promise/try`. Often replaceable with a small local helper. |
| Sync Iterator helpers | Lazy pipelines: `Iterator.from(iter).map(...).filter(...).take(...).toArray()`. | `core-js/actual/iterator` or narrower iterator modules. Iterators are consumable. |
| JSON Modules | Native JSON module import with attributes. | Module loader/syntax/tooling feature. Let Vite bundle JSON or verify native ESM delivery. |
| Import Attributes | `import data from "./x.json" with { type: "json" }`. | Syntax/tooling feature. Babel supports parsing; bundler/runtime must agree. Older `assert { type: "json" }` is obsolete. |
| RegExp Modifiers | Scoped regex flag changes like `(?i:...)`. | Syntax feature. Use Babel transform or avoid if any target cannot parse. |
| New Set methods | `union`, `intersection`, `difference`, `symmetricDifference`, `isSubsetOf`, `isSupersetOf`, `isDisjointFrom`. | `core-js/actual/set` or targeted `core-js/actual/set/union` etc. Methods accept Set-like objects by protocol. |
| Duplicate named capture groups | Reuse group names across disjoint alternatives. | Syntax/engine feature. Transform or avoid if unsupported. |

Example:

```ts
const escaped = RegExp.escape(query);
const matcher = new RegExp(escaped, 'iu');

const visibleRequired = required.intersection(visible);

const ids = Iterator.from(records)
  .filter(record => record.enabled)
  .map(record => record.id)
  .take(50)
  .toArray();

const result = Promise.try(() => maybeSync());
```

## ES2026 Finished-Proposal Set

Current TC39 finished-proposals data lists these with expected publication year 2026:

| Feature | Use for | Compatibility/polyfill |
| --- | --- | --- |
| Map upsert | `map.getOrInsert(key, value)` and `map.getOrInsertComputed(key, fn)` to replace `has/get/set` boilerplate. | `core-js/actual/map/get-or-insert`, `core-js/actual/map/get-or-insert-computed` if available. Verify exact names. |
| JSON.parse source text access | Preserve original source text for parsed primitives, raw JSON embedding, lossless big-number workflows. | `core-js/actual/json/parse`, `core-js/actual/json/raw-json`, `core-js/actual/json/is-raw-json` if needed. Verify API shape. |
| Iterator sequencing | `Iterator.concat(...items)` for lazy concatenation. | `core-js/actual/iterator/concat` or a small generator fallback. |
| `Uint8Array` base64/hex | `Uint8Array.fromBase64`, `fromHex`, `toBase64`, `toHex`, `setFromBase64`, `setFromHex`. | `core-js` modules or focused byte encoding helpers. Prefer over `atob`/`btoa` once supported because it works on bytes. |
| `Math.sumPrecise` | More accurate floating-point summation for analytics/finance-adjacent totals. | `core-js/actual/math/sum-precise` or a numeric summation helper. Still not decimal arithmetic. |
| `Error.isError` | Cross-realm error branding checks. | `core-js/actual/error/is-error` or a ponyfill, with exact cross-realm limits documented. |
| `Array.fromAsync` | Materialize async iterables into arrays. | `core-js/actual/array/from-async`. Avoid on unbounded streams. |

Example:

```ts
const list = byUser.getOrInsertComputed(userId, () => []);
list.push(item);

const bytes = Uint8Array.fromHex(hashHex);
const encoded = bytes.toBase64();

const total = Math.sumPrecise(values);

const routes = Iterator.concat(coreRoutes, pluginRoutes).toArray();

const chunks = await Array.fromAsync(stream);
```

## Not ES2026 Baseline

As of the current TC39 finished-proposals list checked during skill creation:

- `Temporal` is listed with expected publication year 2027. Use the `temporal-js` skill if the project opts in. Polyfill convention here: `temporal-polyfill`.
- Explicit Resource Management (`using`, `await using`, `Symbol.dispose`, `Symbol.asyncDispose`, `DisposableStack`, `AsyncDisposableStack`, `SuppressedError`) is listed with expected publication year 2027. Treat as syntax-plus-runtime opt-in, not general frontend baseline.

Agents must not state that these are finalized ES2026 just because a research note, browser implementation, or TypeScript support says they exist.

## Syntax Versus Runtime

Runtime API examples:

- `Object.groupBy`
- `Promise.withResolvers`
- `RegExp.escape`
- `Set.prototype.union`
- `Iterator.from`
- `Math.sumPrecise`

Syntax/parse-sensitive examples:

- RegExp `/v` literals and set notation
- RegExp modifiers
- duplicate named capture groups
- import attributes
- `using` / `await using`

Syntax must be parseable by the browser or transformed before delivery. Runtime APIs must exist natively or be polyfilled before use.

## Configuration Recipes

Application with no Babel:

```ts
// vite.config.ts
import { defineConfig } from 'vite';

export default defineConfig({
  build: {
    target: ['chrome121', 'edge121', 'firefox122', 'safari17.4']
  }
});
```

Application with Babel/core-js:

```bash
npm install core-js
npm install --save-dev @babel/core @babel/preset-env
```

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

Targeted app entry polyfills:

```ts
import 'core-js/actual/regexp/escape';
import 'core-js/actual/promise/try';
import 'core-js/actual/set';
import 'core-js/actual/iterator';
import 'core-js/actual/array/from-async';
```

Temporal entry:

```ts
export { Temporal } from 'temporal-polyfill';
```

Library policy:

- Do not mutate globals silently.
- Prefer modern ESM output plus documented required APIs.
- Use `core-js-pure` or local helpers for isolated functionality.
- Provide a compatibility build only when consumers need it.

## Caniuse/MDN Check Procedure

For every non-obvious feature:

1. Search caniuse for the exact feature name and browser versions.
2. Open the MDN page and inspect browser compatibility, syntax notes, and exceptions.
3. Check TC39 finished-proposals for edition/publication year when the edition matters.
4. Check `core-js` docs or source for the exact import path.
5. If syntax is involved, check Babel/Vite/esbuild/TypeScript parse and transform support.
6. Document the decision in code review or project notes.

Feature-detection examples:

```ts
const hasIteratorHelpers =
  typeof Iterator !== 'undefined' &&
  typeof Iterator.from === 'function' &&
  typeof Iterator.prototype?.map === 'function';

const hasUint8ArrayBase64 =
  typeof Uint8Array.fromBase64 === 'function' &&
  typeof Uint8Array.prototype.toBase64 === 'function';

const hasSumPrecise = typeof Math.sumPrecise === 'function';
```

## Review Red Flags

- "Modern browsers support ES2026" without exact feature checks.
- Vite target changed but no runtime polyfill plan.
- Babel and Vite both injecting polyfills.
- `core-js/actual` imported globally in a library.
- `defaults` used despite a concrete browser policy.
- New syntax introduced without parser/transform/browser verification.
- Temporal advice duplicated instead of loading `temporal-js`.
