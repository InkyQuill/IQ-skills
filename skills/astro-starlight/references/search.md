# Search

Use for Pagefind, search exclusions, alternate providers, and search-related build issues.

Source docs: `guides/site-search.mdx`, `reference/configuration.mdx#pagefind`.

## Built-In Pagefind

Starlight uses Pagefind by default for static builds.

```js
starlight({
  pagefind: true,
})
```

Pagefind cannot be enabled when `prerender: false`.

## Exclude a Page

```md
---
title: Content to hide from search
pagefind: false
---
```

## Exclude Part of a Page

```mdx
<div data-pagefind-ignore>
  This content is not indexed.
</div>
```

## Configure Pagefind

```js
starlight({
  pagefind: {
    ranking: {
      termFrequency: 1.0,
      pageLength: 0.75,
    },
    indexWeight: 1.0,
  },
})
```

Multisite search shape:

```js
starlight({
  pagefind: {
    mergeIndex: [
      {
        bundlePath: 'https://docs.example.com/_pagefind/',
        baseUrl: 'https://docs.example.com/',
        indexWeight: 0.8,
      },
    ],
  },
})
```

Verify detailed ranking and `mergeIndex` options against current Pagefind docs when tuning search.

## Disable Search

```js
starlight({
  pagefind: false,
})
```

This disables indexing and hides the default search UI.

## Algolia DocSearch

Starlight docs list Algolia DocSearch as the official alternate provider. Before adding it, verify current package name, config shape, and credentials guidance.

Checklist:

- Use a search-only API key in client-visible config.
- Do not commit admin keys.
- Confirm crawler/index settings include localized routes if i18n is enabled.
- Confirm Pagefind is disabled or replaced according to the plugin docs.

## Community Search Providers

The resources page lists community alternatives, including Typesense-powered DocSearch. Treat these as version-sensitive:

1. Read the plugin README/current docs.
2. Check package compatibility with installed Astro/Starlight.
3. Run build and test search UI.

## Verification

Run a production build. Then inspect output and routes:

```bash
npm run build
find dist -maxdepth 3 -iname "*pagefind*" -print
```

For private content, search generated output or use the built site UI to confirm excluded text is absent.
