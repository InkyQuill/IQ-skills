# Skill Authoring Notes

## Structure

Use this layout for portable skills:

```txt
skills/<skill-name>/
  SKILL.md
  references/        # optional heavy docs
  scripts/           # optional reusable tools
  assets/            # optional static resources
  evals/             # optional test scenarios
```

## Frontmatter

Required:

```yaml
---
name: my-skill
description: Use when specific trigger conditions apply.
---
```

Guidelines:

- `name` should be lowercase and hyphenated.
- `description` should answer when to load the skill.
- Avoid workflow summaries in the description; agents may shortcut and skip the body.

## Progressive Disclosure

Keep `SKILL.md` optimized for scanning:

- Core principle.
- When to use.
- Quick reference.
- Required workflow or checklist.
- Common mistakes.

Move long API details, compatibility matrices, and extended examples into `references/`.

## Testing

Use realistic prompts that previously caused bad output:

- Baseline without the skill.
- Same prompt with the skill.
- Refine the skill when the agent still misses key constraints.

For reference skills, verify that an agent can retrieve the right fact and apply it correctly.
