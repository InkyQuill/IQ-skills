# Setup and Project Structure

Use for new projects, adding Starlight to existing Astro projects, and diagnosing basic project shape.

Source docs: `getting-started.mdx`, `manual-setup.mdx`, `guides/project-structure.mdx`.

## New Project

Use the repository's package manager when known:

```bash
npm create astro@latest -- --template starlight
pnpm create astro --template starlight
yarn create astro --template starlight
```

Then run the local dev script:

```bash
npm run dev
pnpm dev
yarn dev
```

Add docs pages under `src/content/docs/`.

## Existing Astro Project

Prefer the Astro integration helper:

```bash
npx astro add starlight
pnpm astro add starlight
yarn astro add starlight
```

Manual minimum:

```bash
npm install @astrojs/starlight
```

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';
import starlight from '@astrojs/starlight';

export default defineConfig({
  integrations: [
    starlight({
      title: 'My docs site',
    }),
  ],
});
```

Then configure content collections:

```ts
// src/content.config.ts
import { defineCollection } from 'astro:content';
import { docsLoader } from '@astrojs/starlight/loaders';
import { docsSchema } from '@astrojs/starlight/schema';

export const collections = {
  docs: defineCollection({ loader: docsLoader(), schema: docsSchema() }),
};
```

## Project Structure

Canonical docs layout:

```txt
astro.config.mjs
src/
  content.config.ts
  content/
    docs/
      index.md
      guides/
        getting-started.md
```

Common additions:

```txt
src/
  assets/             # imported images/logos
  components/         # custom Astro components and overrides
  content/
    i18n/             # UI translation JSON/YAML files
  pages/              # custom Astro routes outside docs collection
  styles/             # customCss files
public/
  favicon.svg
```

## Add Content

Minimal homepage:

```md
---
title: My docs
description: Learn more about my project.
---

Welcome to my project.
```

Every Markdown, MDX, or Markdoc file in `src/content/docs/` becomes a page. File path controls route unless a custom content ID/slug is configured.

## Existing Site Subpath

If all docs should live under a subpath, put docs in a matching subdirectory:

```txt
src/content/docs/guides/index.md   -> /guides/
src/content/docs/guides/api.md     -> /guides/api/
```

This is the documented workaround for adding a docs section to an existing Astro site.

## SSR and Prerender

Starlight pages are pre-rendered by default even if the Astro project uses SSR. To render Starlight pages on demand with an adapter, set:

```js
starlight({
  prerender: false,
})
```

Pagefind cannot be enabled when `prerender: false`.

For Cloudflare SSR, also check the current Cloudflare adapter docs for required compatibility flags.
