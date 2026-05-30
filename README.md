# IQ Skills

Reusable Agent Skills maintained by Pavel Obruchnikov / InkyQuill.

This repository follows the common Agent Skills layout: each skill is a self-contained directory with a `SKILL.md` file and optional supporting files such as `references/`, `scripts/`, `assets/`, or `evals/`.

## Skills

| Skill | Use When |
| --- | --- |
| [es2026-modern-javascript](./skills/es2026-modern-javascript/SKILL.md) | Writing, reviewing, or configuring modern JavaScript/TypeScript with ES2024, ES2025, ES2026, Vite, Babel, core-js, browserslist, caniuse, or polyfills. |
| [temporal-js](./skills/temporal-js/SKILL.md) | Working with the Temporal date/time namespace, immutable Temporal objects, TypeScript Temporal libs, Safari support, and `temporal-polyfill`. |

## Install

With the open skills CLI:

```bash
npx skills add InkyQuill/IQ-skills
```

Install a specific skill:

```bash
npx skills add InkyQuill/IQ-skills --skill temporal-js
npx skills add InkyQuill/IQ-skills --skill es2026-modern-javascript
```

Install globally for Codex:

```bash
npx skills add InkyQuill/IQ-skills -g -a codex --skill '*'
```

Install from a local checkout:

```bash
npx skills add ~/Development/IQ-skills --list
npx skills add ~/Development/IQ-skills --skill temporal-js -g -a codex
```

## Manual Install

Codex global skills:

```bash
mkdir -p ~/.codex/skills
cp -R skills/temporal-js ~/.codex/skills/
cp -R skills/es2026-modern-javascript ~/.codex/skills/
```

Project-local universal skills:

```bash
mkdir -p .agents/skills
cp -R skills/temporal-js .agents/skills/
cp -R skills/es2026-modern-javascript .agents/skills/
```

Claude Code global skills:

```bash
mkdir -p ~/.claude/skills
cp -R skills/temporal-js ~/.claude/skills/
```

## Repository Layout

```txt
skills/
  temporal-js/
    SKILL.md
    references/
  es2026-modern-javascript/
    SKILL.md
    references/
template/
  SKILL.md
docs/
  authoring.md
AGENTS.md
```

## Create A New Skill

1. Copy `template/SKILL.md` into `skills/<skill-name>/SKILL.md`.
2. Keep the `name` lowercase with hyphens.
3. Make the `description` start with `Use when...` and describe trigger conditions, not the workflow.
4. Put heavy API details in `references/` and keep `SKILL.md` focused.
5. Test the skill against realistic prompts before publishing.

## Compatibility

These skills use the shared `SKILL.md` convention used by Codex, Claude Code, Cursor, OpenCode, and other agent tools. Some agents only read `SKILL.md`; avoid depending on agent-specific metadata unless the skill explicitly says so.

## License

MIT. See [LICENSE](./LICENSE).
