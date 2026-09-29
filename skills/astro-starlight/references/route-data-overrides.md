# Route Data and Overrides

Use for component overrides, Starlight route middleware, custom route data, and custom pages that need Starlight layout.

Source docs: `guides/overriding-components.mdx`, `reference/overrides.md`, `guides/route-data.mdx`, `reference/route-data.mdx`, `guides/pages.mdx`.

## Override Only When Needed

Use component overrides when config, frontmatter, and CSS cannot solve the requirement.

```js
starlight({
  components: {
    SocialLinks: './src/components/MySocialLinks.astro',
  },
})
```

Before overriding:

- Read the current overrides reference for component names and props.
- Prefer wrapping the default component to preserve behavior.
- Keep overrides small and focused.
- Verify accessibility after changes.

## Reuse Built-In Components

```astro
---
import Default from '@astrojs/starlight/components/Header.astro';
---

<Default {...Astro.props}>
  <slot />
</Default>
```

Use the exact default component path from the current overrides reference.

## Override Categories

The overrides reference groups components by:

- Head.
- Accessibility.
- Layout.
- Header.
- Global Sidebar.
- Page Sidebar.
- Content.
- Footer.

Choose the narrowest override point. For example, avoid overriding a whole layout if a footer or social links override is enough.

## Route Data

Starlight uses a `starlightRoute` object to render pages. Important fields include:

| Field | Purpose |
| --- | --- |
| `dir`, `lang`, `locale` | Writing direction, language tag, locale key. |
| `siteTitle`, `siteTitleHref` | Site title metadata/navigation. |
| `id` | Content entry ID. |
| `isFallback` | Whether fallback i18n content is used. |
| `entry`, `entryMeta` | Content collection entry and metadata. |
| `sidebar` | Resolved sidebar tree. |
| `hasSidebar` | Whether sidebar renders. |
| `pagination` | Previous/next links. |
| `toc`, `headings` | Table of contents and headings. |
| `lastUpdated`, `editUrl`, `head` | Footer/head metadata. |

Use the route data reference for exact type details before relying on a field.

## Route Middleware

Route middleware can customize Starlight route data before rendering.

Config:

```js
starlight({
  routeMiddleware: './src/starlight-route-middleware.ts',
})
```

Middleware shape:

```ts
import { defineRouteMiddleware } from '@astrojs/starlight/route-data';

export const onRequest = defineRouteMiddleware((context) => {
  return context.next();
});
```

Verify current import path and callback API against the route data docs before implementing non-trivial middleware.

## Custom Pages with Starlight Layout

For `src/pages/*.astro`, use `StarlightPage`:

```astro
---
import StarlightPage from '@astrojs/starlight/components/StarlightPage.astro';
---

<StarlightPage frontmatter={{ title: 'Custom' }}>
  <p>Custom page content.</p>
</StarlightPage>
```

Use `AnchorHeading` for Starlight-style heading anchors:

```astro
---
import AnchorHeading from '@astrojs/starlight/components/AnchorHeading.astro';
---

<AnchorHeading level="2" id="details">Details</AnchorHeading>
```

## Validation

- Build the site.
- Inspect generated route and sidebar.
- Check keyboard navigation and landmarks when overriding layout/header/sidebar/footer.
- Re-check localized route behavior if `dir`, `lang`, `locale`, or fallback data is touched.
