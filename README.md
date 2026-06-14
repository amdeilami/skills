# skills

A personal package of [agent skills](https://skills.sh) — reusable instructions an AI agent can
load on demand. Each skill is a folder with a `SKILL.md`. New skills get added here over time.

## Skills

| Skill | What it does |
| --- | --- |
| [`prompt-refinery`](./prompt-refinery/SKILL.md) | Rewrites a verbose or ambiguous prompt into a tighter, more accurate version — preserving all literal data verbatim and asking before guessing on blocking ambiguities. It refines the prompt; it never executes it. |

## Install

Install everything in this package:

```bash
npx skills add <your-github-username>/skills
```

List what's available without installing:

```bash
npx skills add <your-github-username>/skills --list
```

Install a single skill:

```bash
npx skills add <your-github-username>/skills/tree/main/prompt-refinery
```

Add `-g` to install globally instead of into the current project.

## Discovery

There is no manual submission step for [skills.sh](https://skills.sh) — repositories are indexed
automatically via install telemetry once someone runs `npx skills add` against this repo.

## Authoring a new skill

1. Create a folder named in `kebab-case`.
2. Add a `SKILL.md` whose frontmatter `name` **exactly matches the folder name** (lowercase
   letters, numbers, hyphens only) and includes a `description` stating *what* it does and *when*
   to use it.
3. Optionally add `scripts/`, `references/`, or `assets/` subfolders.
