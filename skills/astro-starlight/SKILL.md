---
name: astro-starlight
description: Use when creating, editing, reviewing, migrating, or troubleshooting Astro Starlight documentation sites, including Starlight setup, docs architecture, Markdown/MDX authoring, src/content.config.ts collections, sidebar navigation, Pagefind/search, i18n, theming, component overrides, route data, Starlight components, and Starlight plugins.
---

# Astro Starlight

## Operating Model

Use the APEX-style workflow adapted from fuse-ai-pilot: Analyze, Plan, Execute, Verify, eXamine. Do this autonomously with normal tools. Do not require Claude Code, fuse-ai-pilot, Context7, Exa, or any specific agent runtime.

This skill is based on:

- Starlight docs source cloned from `withastro/starlight`, commit `cb26a3b`.
- fuse-ai-pilot workflow source cloned from `fusengine/agents`, commit `7749d4d`.
- Official Starlight docs at https://starlight.astro.build/.

## Mandatory Workflow

For any non-trivial Starlight task, load [references/workflow.md](references/workflow.md) first.

1. Analyze: inspect the local project and relevant Starlight source/reference docs.
2. Plan: define files, acceptance criteria, verification commands, and risk.
3. Execute: make small scoped edits following local conventions.
4. Verify: run the cheapest meaningful checks, then broader checks when behavior or config changed.
5. eXamine: review usages, routes, build output, docs navigation, and user request coverage before finalizing.

For a very small docs text edit, you may collapse the workflow, but still inspect local conventions and verify frontmatter/link behavior when affected.

## Reference Router

Start with [references/index.md](references/index.md). It maps Starlight docs source pages to local reference files.

| Task | Read |
| --- | --- |
| New or existing Starlight setup | [references/setup.md](references/setup.md), [references/configuration.md](references/configuration.md) |
| Page creation, routing, frontmatter, schema | [references/content.md](references/content.md), [references/frontmatter.md](references/frontmatter.md) |
| Markdown/MDX authoring and built-in components | [references/authoring-components.md](references/authoring-components.md) |
| Sidebar/navigation | [references/sidebar.md](references/sidebar.md) |
| Search/Pagefind/DocSearch | [references/search.md](references/search.md) |
| i18n/locales/UI strings | [references/i18n.md](references/i18n.md) |
| Branding, CSS, Tailwind, overrides | [references/customization.md](references/customization.md), [references/route-data-overrides.md](references/route-data-overrides.md) |
| Plugins and plugin authoring | [references/plugins.md](references/plugins.md) |

## Defaults

| Need | Default |
| --- | --- |
| Docs pages | `src/content/docs/` using Markdown or MDX. |
| Content config | `src/content.config.ts` with `docsLoader()` and `docsSchema()`. |
| Navigation | Filesystem-generated sidebar first; manual sidebar only when information architecture needs it. |
| Search | Built-in Pagefind unless the project explicitly needs another provider. |
| Styling | `customCss` and Starlight CSS variables before component overrides. |
| i18n | `locales` config plus mirrored content paths; add `i18nLoader()`/`i18nSchema()` for UI strings. |
| Plugin APIs | Verify package README/docs and installed version before use. |

## Hard Rules

- Do not guess Starlight API shapes. Check local installed versions and current docs/reference files.
- Do not replace Starlight collection schemas. Extend `docsSchema()` or `i18nSchema()`.
- Do not use generic Astro content loaders for Starlight docs unless intentionally implementing a custom non-Starlight collection.
- Do not use `slug` for external URLs. Use `{ label, link }`.
- Do not add manual sidebar config when frontmatter order/labels or filesystem organization solves the problem.
- Do not override Starlight components until config and CSS options are insufficient.
- Do not add community plugin examples from memory. Verify package names, option shapes, and compatibility.

## Final Check

Before reporting completion, include:

- What references were consulted.
- What files changed.
- What validation ran and its result.
- Any unchecked risk, especially skipped build, unverified plugin API, or missing package install.
