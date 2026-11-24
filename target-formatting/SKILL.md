---
name: target-formatting
description: >-
  Use this skill whenever formatting or providing a copy-pasteable block of
  text, Markdown, or code for a specific target surface (such as a GitHub
  issue/PR comment, Jira comment, Slack message, Discord message, Google Doc or
  comment, Notion page, Teams chat, email, plain text, or terminal/bash
  command). Ensures syntax, structure, and copy-paste wrapping match the
  destination while preserving the original content.
license: MIT
metadata:
  version: 0.1.0
---

# Target-Aware Copy-Ready Formatting

Format text or code so the user can copy and paste it cleanly into the target destination without broken syntax, mangled structure, or manual cleanup.

## Core Rules

1. **Preserve content faithfully:** Format only. Fix obvious spelling or typo mistakes, but never rewrite, expand, summarize, or change the user's meaning or wording unless explicitly asked.
2. **Isolate the copyable payload:** Never put conversational remarks ("Here you go:", "Let me know...") inside the copyable block. Keep any brief note strictly outside it.
3. **Never let inner code fences break the outer block:** If a copyable Markdown block itself contains triple backticks (`` ``` ``), wrap the outer block in **4 backticks** (````` ````markdown ... ```` `````) — or `N + 1` backticks where `N` is the longest inner fence.
4. **Match the copy-paste mechanism:**
   - **Raw-syntax / Code / Plain-text targets** (GitHub, Jira Server/DC Wiki, Slack `mrkdwn`, Discord, Notion, WhatsApp, Google Docs comments, Plain text, Bash/Code): put inside a fenced code block so the UI offers one-click raw copy and does not double-render `*bold*` as *italic*.
   - **Rich-text-paste targets** (Jira Cloud WYSIWYG editor, Google Docs body, Gmail/Outlook rich email, Microsoft Teams compose): pasting raw Markdown from a code block often leaves literal `**` or `#` behind. Output clean, standard Markdown **outside** a code block so highlight-and-copy grabs rendered rich text, and add a 1-line note above (e.g., *"Highlight and copy below for Jira Cloud"*). If the user explicitly asks for raw Markdown, provide a fenced block instead.

---

## Target Rules

### GitHub (PR / Issue / Discussion / Comment)
- **Wrapper:** Fenced `markdown` block (use 4+ backticks if it contains inner code fences).
- **Syntax:** Full GitHub-Flavored Markdown (`**bold**`, `*italic*`, `~~strike~~`, `#`–`######`, `[label](url)`, pipe tables, `- [ ]` task lists, ```` ```lang ````, ```` ```diff ````, ```` ```suggestion ```` for PR review suggestions).
- **Extras:** Callouts (`> [!NOTE]`, `[!TIP]`, `[!IMPORTANT]`, `[!WARNING]`, `[!CAUTION]`) and collapsible `<details><summary>Title</summary>...</details>` are natively supported.

### Jira (Comments & Descriptions)
Jira has **two different editors** — confirm or note which one applies if unclear:
- **Jira Cloud (Modern WYSIWYG / ADF editor):**
  - **Wrapper:** Rendered Markdown **outside** a code block (for rich-text copy-paste). Pasting raw Markdown from a code block often renders literal symbols.
  - **Supported:** Headings, bold, italic, lists, links, blockquotes, fenced code blocks.
  - **Avoid:** Raw HTML tags, GitHub alerts (`> [!NOTE]`), complex tables (simplify to bullet lists if needed).
- **Jira Server / Data Center (Wiki Markup):**
  - **Wrapper:** Fenced `text` block.
  - **Syntax:**
    - Bold: `*text*` (single asterisk) | Italic: `_text_` | Strike: `-text-` | Inline code: `{{code}}`
    - Headings: `h1. Title` through `h6. Title`
    - Links: `[label|https://...]` (label first, pipe, URL)
    - Lists: `* item` / `** nested` | Numbered: `# item` / `## nested`
    - Quotes: `bq. single line` or `{quote}...{quote}`
    - Code block: `{code:bash} ... {code}` (or `{noformat}...{noformat}` for plain logs)
    - Tables: `||Header 1||Header 2||` and `|Cell 1|Cell 2|`

### Code & Shell Commands (`bash`, `sql`, `json`, etc.)
- **Wrapper:** Fenced code block tagged with the exact language (`bash`, `sh`, `python`, `json`, `yaml`, `diff`, etc.).
- **Runnable shell rules:**
  - Omit leading `$ ` or `# ` prompt characters so commands paste and run cleanly.
  - Do not mix command output into a runnable command block; place output in a separate `text` block if needed.
  - Mark placeholders unmistakably (e.g., `<PROJECT_ID>`).

### Chat Apps (Slack, Discord, Teams, WhatsApp, Google Chat)
- **Slack (`mrkdwn` — UI paste):** Fenced `text` block. `*bold*`, `_italic_`, `~strike~`, `` `code` ``, ```` ```block``` ````, `> quote`, `- ` bullets. No `#` headings (use `*SECTION*`), no pipe tables (use bullet cards), use raw URLs (auto-link cleanly).
- **Discord:** Fenced `markdown` block (4+ backticks if nested). `**bold**`, `*italic*`, `__underline__` (not bold!), `~~strike~~`, `||spoiler||`, `#`–`###`, `-# subtext`, `[label](url)`, `<url>` (suppress embed), ```` ```lang ````, `> ` / `>>> `. No tables. Limit: 2,000 chars (4,000 Nitro).
- **Microsoft Teams (Chat):** Does not parse pasted raw Markdown. Emit rendered Markdown **outside** a code block for rich-text copy-paste; avoid tables.
- **WhatsApp & Google Chat:** Fenced `text` block. `*bold*`, `_italic_`, `~strike~`, `` `code` ``, ```` ```block``` ````, `- ` / `1. `, `> quote`. No spaces inside markers (`* bold *` breaks). No headings, tables, or `[label](url)` links.

### Docs, Email & Plain Text
- **Google Docs (Body):** Emit rendered Markdown **outside** a code block for rich-text copy-paste (avoid pipe tables/code fences if the user plans to use *Paste from Markdown*).
- **Google Docs (Comment Box):** Fenced `text` block. Only `*bold*`, `_italic_`, `_*bold italic*_`, `-strike-`, and **raw URLs** (`[label](url)` breaks). No headings, tables, or code blocks.
- **Notion:** Fenced `markdown` block (4+ backticks if nested). `#`–`####` (space after `#` required), standard inline Markdown, lists, checklists, links, code blocks. Avoid raw HTML, `> [!NOTE]` alerts, and complex pipe tables.
- **Email (Rich — Gmail/Outlook):** Rendered Markdown **outside** a code block; stick to short paragraphs, bold, bullets, and links (avoid complex tables and raw code fences).
- **Plain Text / SMS / iMessage / Social (LinkedIn, X):** Fenced `text` block. Zero Markdown syntax and zero Unicode "fake bold" (`𝗯𝗼𝗹𝗱`, which breaks screen readers and search). Use blank lines, `SECTION:` or `ALL CAPS`, `- ` bullets, and `Label: https://...`.

---

## Graceful Degradation (When a Feature Is Unsupported)

Degrade structurally without dropping any information:

| Unsupported Element | Replacement |
| :--- | :--- |
| **Table** | Bullet cards (`*Item* — Key: val \| Key: val`) or grouped lines. Avoid ASCII-art tables (wrapping breaks them on mobile). |
| **Headings** | Blank line + bold title (`*Section*`) or `SECTION:` in plain text. |
| **Masked link `[Label](url)`** | `Label: https://...` (never drop the URL). |
| **Callout (`> [!WARNING]`)** | `> ⚠️ *Warning:* ...` (quote-capable) or `WARNING: ...` (plain text). |
| **Checkboxes (`- [x]` / `- [ ]`)** | `✅` / `⬜` (or `[Done]` / `[Todo]` in formal plain text). |
| **Over character limit** | Keep wording intact; split cleanly at paragraph boundaries (`1/2`, `2/2`) only when required. |

---

## Quick Verification Before Responding

- [ ] Original meaning and wording preserved (only obvious typos fixed)?
- [ ] Conversational text kept outside the copyable block?
- [ ] Outer code fence uses strictly more backticks than any inner code fence?
- [ ] Correct copy mode used (fenced block for raw-syntax/code/plain targets vs. rendered outside a fence for rich-text targets like Jira Cloud, Docs body, Teams, rich email)?
- [ ] Zero leaked syntax for the target (no `**` in Slack/Jira Wiki, no `[a](b)` in WhatsApp/Docs comments, no pipe tables in Slack/Discord/Teams, no `$ ` prompts in runnable bash)?
