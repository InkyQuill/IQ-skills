# Publishing

## Create GitHub Repository

The intended remote is:

```txt
git@github.com:InkyQuill/IQ-skills.git
```

With GitHub CLI authenticated:

```bash
gh auth login
gh repo create InkyQuill/IQ-skills --public --source=. --remote=origin --push
```

If the repository was created manually on GitHub:

```bash
git remote set-url origin git@github.com:InkyQuill/IQ-skills.git
git push -u origin main
```

## Validate Skill Discovery

```bash
npx skills add . --list
```

Expected skills:

- `es2026-modern-javascript`
- `temporal-js`

## Install From GitHub

After publishing:

```bash
npx skills add InkyQuill/IQ-skills --list
npx skills add InkyQuill/IQ-skills --skill temporal-js -g -a codex
```
