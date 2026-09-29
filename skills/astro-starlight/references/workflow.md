# Workflow

This is the agent-agnostic equivalent of fuse-ai-pilot's APEX flow for Starlight docs work. It keeps the useful discipline without depending on Claude Code, TeamCreate, fuse-ai-pilot agents, or MCP-specific tools.

## Phase A: Analyze

Run local exploration and documentation research before editing.

Project exploration:

```bash
pwd
ls -la
find . -maxdepth 2 \( -name package.json -o -name "astro.config.*" -o -name "tsconfig.json" -o -name "src" \) -print
find src -maxdepth 3 \( -name "content.config.*" -o -path "*/content/docs/*" -o -path "*/pages/*" \) -print 2>/dev/null | head -100
```

Read, when present:

- `package.json` scripts and dependencies.
- Lockfile to infer package manager.
- `astro.config.*`.
- `src/content.config.ts` or legacy `src/content/config.ts`.
- Existing docs pages near the requested area.
- Existing custom CSS, overrides, route middleware, or plugin config.

Documentation research:

- Use [index.md](index.md) to select local references.
- For unstable plugin APIs or unknown Starlight options, verify against upstream docs or the installed package.
- Prefer official Starlight/Astro docs over tutorials.

Output mentally or in notes:

- Relevant files and conventions.
- Installed Astro/Starlight/package versions.
- Required Starlight docs references.
- Change locations and likely verification commands.

## Phase P: Plan

For anything beyond a small copy edit, write or maintain a short task plan:

- Files to modify/create.
- Content/navigation/config behavior expected.
- Acceptance criteria from the user's request.
- Validation commands, e.g. `npm run build`, `pnpm astro check`, docs link checker.
- Risks such as generated docs, i18n fallback behavior, SSR/prerender constraints, plugin compatibility, or custom schema migrations.

Plan edits in this order:

1. Content schema/config.
2. Content files.
3. Sidebar/navigation.
4. Styling/components/overrides.
5. Tests/build/link validation.

## Phase E: Execute

Follow the local project style.

- Keep edits scoped to docs/Starlight files.
- Preserve existing package manager conventions.
- Add frontmatter before body content when creating pages.
- Prefer frontmatter controls (`sidebar.order`, `sidebar.label`, `pagefind`, `tableOfContents`) before global config changes.
- Prefer `customCss` before component overrides.
- Keep examples runnable and version-appropriate.

When unexpected complexity appears, stop and re-analyze before expanding scope.

## Phase V: Verify Functional Resolution

Re-read the user's request and check each requirement:

- Does the content appear at the intended route?
- Is the sidebar/search/i18n behavior correct?
- Are frontmatter fields valid under `docsSchema()`?
- Are generated or custom pages handled correctly?
- Are links route-correct for the project's `base`, `site`, and locale setup?

Use evidence: command output, file inspection, route path derivation, or source docs.

## Phase X: eXamine

Perform the equivalent of ai-pilot's sniper validation:

1. Re-scan structure and changed files.
2. Re-check the relevant official reference.
3. Search usages and affected routes.
4. Run formatter/typecheck/build/link checks available in the project.
5. Fix issues minimally.
6. Re-run failed checks until clean or report the blocker.

Useful commands:

```bash
rg -n "starlight|docsLoader|docsSchema|i18nLoader|i18nSchema|sidebar|pagefind" astro.config.* src package.json
find src/content/docs -type f \( -name "*.md" -o -name "*.mdx" -o -name "*.mdoc" \) | sort
npm run build
pnpm build
npm run astro -- check
pnpm astro check
```

Use whichever commands match the project. Do not invent scripts that are not present.
