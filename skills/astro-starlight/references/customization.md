# Customization, CSS, and Theming

Use for branding, logos, sitemap, layout, table of contents, social/edit links, 404 pages, fonts, CSS, Tailwind, and theme colors.

Source docs: `guides/customization.mdx`, `guides/css-and-tailwind.mdx`, `reference/configuration.mdx`.

## Customization Order

1. Built-in config options.
2. Page frontmatter.
3. `customCss` and CSS variables.
4. Tailwind integration if already in use or explicitly needed.
5. Component overrides only when config/CSS cannot solve it.

## Logo

```js
starlight({
  logo: {
    src: './src/assets/logo.svg',
    alt: 'Project logo',
    replacesTitle: true,
  },
})
```

Light/dark variants:

```js
starlight({
  logo: {
    light: './src/assets/logo-light.svg',
    dark: './src/assets/logo-dark.svg',
    alt: 'Project logo',
  },
})
```

## Sitemap

Set Astro's `site` option:

```js
export default defineConfig({
  site: 'https://docs.example.com',
  integrations: [starlight({ title: 'Docs' })],
});
```

## Page Layout

Per-page splash layout:

```md
---
title: My Landing Page
template: splash
---
```

TOC global:

```js
starlight({
  tableOfContents: { minHeadingLevel: 2, maxHeadingLevel: 4 },
})
```

TOC per page:

```md
---
title: Page without TOC
tableOfContents: false
---
```

## Social and Edit Links

```js
starlight({
  social: [
    { icon: 'github', label: 'GitHub', href: 'https://github.com/example/project' },
  ],
  editLink: {
    baseUrl: 'https://github.com/example/project/edit/main/',
  },
})
```

## Custom 404

Use Starlight content page:

```md
---
title: '404'
template: splash
---

Page not found.
```

Or disable Starlight's default route and provide `src/pages/404.astro`:

```js
starlight({
  disable404Route: true,
})
```

## Custom CSS

```js
starlight({
  customCss: ['./src/styles/starlight.css'],
})
```

```css
:root {
  --sl-color-accent: #2563eb;
  --sl-color-accent-high: #1d4ed8;
  --sl-color-accent-low: #dbeafe;
  --sl-font: Inter, system-ui, sans-serif;
}

:root[data-theme='dark'] {
  --sl-color-accent: #60a5fa;
  --sl-color-accent-high: #bfdbfe;
  --sl-color-accent-low: #172554;
}
```

Keep contrast valid in both themes. Prefer semantic Starlight variables over hard-coded page colors.

## Cascade Layers

Starlight uses CSS layers. When custom styles fail to override Starlight, check layer order and selector specificity before adding `!important`.

## Tailwind

Use Tailwind when the project already uses it or the docs require utility styling. Follow current Astro/Starlight/Tailwind docs for version-specific setup.

Typical concerns:

- Ensure Tailwind scans docs content and components.
- Avoid collisions with Starlight's base styles.
- Keep Starlight CSS variables as the theme bridge where possible.

## Fonts

Starlight supports local CSS or installed font packages through `customCss`:

```js
starlight({
  customCss: ['@fontsource/inter', './src/styles/starlight.css'],
})
```

```css
:root {
  --sl-font: Inter, system-ui, sans-serif;
}
```

## Head Tags

Use `head` for static tags:

```js
starlight({
  head: [
    { tag: 'meta', attrs: { name: 'theme-color', content: '#2563eb' } },
  ],
})
```

`head` entries are emitted directly and do not go through Astro script/style processing. Use a Head override if local imports or Astro processing are required.
