# Authoring and Components

Use for Markdown/MDX authoring, Expressive Code, Markdoc, and Starlight built-in components.

Source docs: `guides/authoring-content.mdx`, `components/*.mdx`, `reference/icons.mdx`.

## Authoring Basics

Starlight supports Markdown and MDX content pages without extra config. Use MDX when you need components.

```md
---
title: Markdown Guide
description: How to use Markdown in Starlight.
---

## Introduction

Content here.
```

Use one H1 equivalent per page; Starlight renders the page title.

## Asides

Markdown directive syntax:

```md
:::note
Useful information.
:::

:::tip[Custom title]
Actionable advice.
:::
```

Aside types: `note`, `tip`, `caution`, `danger`. For custom icons/titles in MDX, use the `<Aside>` component.

## Code Blocks

Starlight uses Expressive Code.

````md
```js title="astro.config.mjs" ins={4} del={2}
import starlight from '@astrojs/starlight';
```
````

Useful features:

- File titles.
- Line and word highlighting.
- Inserted/deleted line markers.
- Terminal frames.
- Collapsible long code blocks depending on Expressive Code config.

## Details, Footnotes, and Common Markdown

Use standard Markdown for blockquotes, lists, tables, links, images, and footnotes. For collapsible details, use Starlight's documented syntax or raw HTML depending on project conventions.

## Markdoc

Starlight can support Markdoc, but it is not default. Use current Starlight authoring docs before adding Markdoc to an existing project, because setup touches dependencies, content config, and Markdown processing.

## MDX Component Imports

```mdx
---
title: Welcome
---

import { Card, CardGrid } from '@astrojs/starlight/components';

<CardGrid>
  <Card title="Fast">Useful content.</Card>
</CardGrid>
```

Built-in components are imported from `@astrojs/starlight/components`.

## Component Index

| Component | Use |
| --- | --- |
| `Aside` | Secondary callouts with type/title/icon. |
| `Badge` | Inline status labels. |
| `Card` | Boxed content. |
| `CardGrid` | Layout for cards/link cards; supports `stagger`. |
| `Code` | Render syntax-highlighted imported code without Markdown fence. |
| `FileTree` | Directory trees with highlights, comments, placeholders. |
| `Icon` | Starlight icon by name with label/size/color/class. |
| `LinkButton` | Prominent call-to-action links. |
| `LinkCard` | Card-style link previews. |
| `Steps` | Styled numbered task lists. |
| `Tabs`, `TabItem` | Tabbed equivalent content; supports synced tabs. |

## Common Props

`Aside`:

```mdx
<Aside type="tip" title="Tip" icon="rocket">
  Content.
</Aside>
```

`Badge`:

```mdx
<Badge text="New" variant="tip" size="small" />
```

`Card` / `LinkCard`:

```mdx
<Card title="Guides" icon="open-book">Read the docs.</Card>
<LinkCard title="API" href="/reference/" description="API reference." />
```

`CardGrid`:

```mdx
<CardGrid stagger>
  <Card title="One">First.</Card>
  <Card title="Two">Second.</Card>
</CardGrid>
```

`Tabs`:

```mdx
<Tabs syncKey="pkg">
  <TabItem label="npm">npm install</TabItem>
  <TabItem label="pnpm">pnpm add</TabItem>
</Tabs>
```

`Steps`:

```mdx
<Steps>
1. Install.
2. Configure.
3. Build.
</Steps>
```

## Icons

Use `Icon` for built-in Starlight icons:

```mdx
import { Icon } from '@astrojs/starlight/components';

<Icon name="github" label="GitHub" />
```

For config fields like `social.icon` and `hero.actions[].icon`, verify names against the current icons reference.

## Compatibility

Starlight's components are designed for Starlight content styles. When using custom components:

- Keep accessible labels on icons and interactive elements.
- Avoid layout markup that fights Starlight's content width.
- Prefer MDX imports local to pages unless a component is reused broadly.
