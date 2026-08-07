# skills

A personal package of [agent skills](https://skills.sh) — reusable instructions an AI agent can
load on demand. Each skill is a folder with a `SKILL.md`. New skills get added here over time.

## Skills

| Skill | What it does |
| --- | --- |
| [`prompt-refinery`](./prompt-refinery/SKILL.md) | Turns a hastily typed, sloppy, or vague message into a clear prompt, then offers to run it. Fixes obvious typos in paths and names, but carries keys, hashes, and IDs through byte-for-byte, and asks before guessing on blocking ambiguities. Invoke it explicitly with `/prompt-refinery`; it never loads on its own. |

## Install

Install everything in this package:

```bash
npx skills add amdeilami/skills
```

List what's available without installing:

```bash
npx skills add amdeilami/skills --list
```

Install a single skill:

```bash
npx skills add amdeilami/skills/tree/main/prompt-refinery
```

Add `-g` to install globally instead of into the current project.

`prompt-refinery` uses Claude Code–only frontmatter (`disable-model-invocation`, `disallowed-tools`,
`argument-hint`). Those install fine through `npx skills add`, but uploading to claude.ai or the
Skills API requires stripping them — that path accepts only `name`, `description`, `license`,
`compatibility`, `metadata`, and `allowed-tools`, and hard-errors on anything else.

## Local development

Claude Code discovers skills in `~/.claude/skills/` (personal) and `.claude/skills/` (project) — not
from wherever this repo happens to be cloned. Symlink a skill instead of installing it, so edits here
take effect without reinstalling:

```bash
ln -sfn "$PWD/prompt-refinery" ~/.claude/skills/prompt-refinery
```

Skills are discovered at startup, so restart Claude Code afterward.

## Discovery

There is no manual submission step for [skills.sh](https://skills.sh) — repositories are indexed
automatically via install telemetry once someone runs `npx skills add` against this repo.

## Authoring a new skill

1. Create a folder named in `kebab-case`.
2. Add a `SKILL.md` whose frontmatter `name` **exactly matches the folder name** (lowercase
   letters, numbers, hyphens only) and includes a `description` stating *what* it does and *when*
   to use it.
3. Optionally add `scripts/`, `references/`, or `assets/` subfolders.
