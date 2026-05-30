# AGENTS.md

This repository is a personal Agent Skills collection.

## Purpose

- Keep skills portable across Codex, Claude Code, Cursor, OpenCode, and other tools that understand `SKILL.md`.
- Prefer the canonical layout `skills/<skill-name>/SKILL.md`.
- Keep each skill self-contained with optional `references/`, `scripts/`, `assets/`, or `evals/` inside the skill directory.

## Authoring Rules

- Skill names use lowercase letters, numbers, and hyphens.
- `SKILL.md` frontmatter must include `name` and `description`.
- Descriptions start with `Use when...` and describe trigger conditions.
- Do not summarize the whole workflow in the description.
- Main `SKILL.md` files should be concise and point to references for heavy details.
- Prefer primary sources for technical reference skills.
- For browser/platform support, require exact feature checks with current docs rather than broad edition claims.

## Validation

Before considering a skill ready:

```bash
find skills -name SKILL.md -print
```

Check each `SKILL.md` has valid frontmatter:

```bash
grep -R --line-number '^name: \|^description: ' skills/*/SKILL.md
```

If the open skills CLI is available:

```bash
npx skills add . --list
```

## Editing

- Do not rewrite skill content casually; preserve tested behavior.
- Add references instead of bloating `SKILL.md` when details exceed scanning size.
- Keep examples runnable and concrete.
- Avoid legacy-browser guidance unless a skill explicitly targets legacy compatibility.
