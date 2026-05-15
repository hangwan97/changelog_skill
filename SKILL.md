---
name: changelog_skill
description: 'Draft a new GitHub Copilot IDE changelog entry (JetBrains, Eclipse, Xcode) for this repository. USE WHEN: the user asks to "create a changelog", "draft a release note", "write a new changelog entry", "announce a Copilot feature", or pastes raw release info that needs formatting. INTERVIEWS the user for title, release date, target product(s) (JetBrains / Eclipse / Xcode — multi-select), and optional main context, then produces the correctly named file (YYYY-MM-DD-Title.md), YAML frontmatter, and a body scaffolded with the standard sections (poster image, ✨ What''s new, body, 💬 Share your feedback with product-specific channels). DO NOT USE for: editing unrelated docs, generating product code, or writing changelogs for non-Copilot products.'
argument-hint: '[title] [release-date YYYY-MM-DD]'
---

# Changelog Skill

Generates a new changelog entry for the GitHub Copilot IDE extensions (JetBrains, Eclipse, Xcode) following this repository's established conventions.

## Quick start (for contributors who just cloned this repo)

This skill ships with a ready-to-use `/changelog` slash command.

In Copilot Chat, you can either:

- Type **`/changelog`** (with optional inline args), e.g.
  `/changelog Custom Agent for Xcode now GA, releasing 2026-05-20`, or
- Ask in plain English:
  > create a new changelog for *Custom Agent for Xcode now GA, releasing 2026-05-20*

Either way, you'll be asked a few quick questions (title, date, target IDE(s),
optional context), then the new file is written at the workspace root.

If `/changelog` does not appear, reload the VS Code window
(`Cmd/Ctrl+Shift+P` → **Developer: Reload Window**) and confirm the setting
`chat.promptFiles` is `true`. Full install steps (including how to copy
[template/changelog.prompt.md.tmpl](./template/changelog.prompt.md.tmpl) into
`.github/prompts/`) are in [README.md](./README.md).

## When to Use

Trigger this skill whenever the user wants to:
- Create / draft / scaffold a new changelog entry or release note
- Announce a Copilot feature for JetBrains, Eclipse, or Xcode (public preview, GA, improvements)
- Convert raw notes / bullets into the canonical changelog format used in this repo

## Inputs to Collect

Use the `vscode_askQuestions` tool (one batched call) to gather the following from the user. **Do not skip this step** — even if the user provided some info up front, confirm what is missing.

| # | Field | Required | Notes |
|---|-------|----------|-------|
| 1 | **Title** | Yes | Full announcement headline. Title-case. May contain colons. Example: `Custom Agent for JetBrains, Eclipse, and Xcode now in public preview` |
| 2 | **Release date** | Yes | `YYYY-MM-DD`. Default suggestion = today. |
| 3 | **Product(s)** | Yes | Which IDE(s) this changelog covers. **Multi-select** from: `JetBrains`, `Eclipse`, `Xcode`. Drives the feedback-channel block and helps validate the title. Ask with `multiSelect: true` and the three options. |
| 4 | **Main context** | Optional | Free-form notes: feature highlights, preview vs GA, links, screenshots. If empty, generate a sensible scaffold the user can fill in. |

If the user already supplied any of these in the prompt, pre-fill that question's default and only ask about the remaining ones.

### Question wording (suggested)

- `title` — *"What is the title of this changelog?"*
- `release-date` — *"What is the release date? (YYYY-MM-DD)"*
- `product` — *"Which product(s) does this changelog cover?"* with `multiSelect: true` and options `JetBrains`, `Eclipse`, `Xcode`.
- `main-context` — *"Any main context, highlights, or notes to include? (optional)"*

## Output Procedure

### Step 1 — Derive the filename

Pattern: `YYYY-MM-DD-<Title>.md`

Rules (matching files already in this repo):
- Use the release date verbatim (e.g. `2025-11-18`).
- Keep the title's original casing and spaces. Do **not** slugify.
- Strip characters that are illegal on macOS/Windows filesystems: `/ \ : * ? " < > |`. Replace `:` with nothing or a hyphen as needed.
- Save the file at the **workspace root** (same level as the existing `YYYY-MM-DD-*.md` entries), not inside `changelog_skill/`.

Examples already in the repo:
- `2025-11-18- Custom Agent for JetBrains, Eclipse, and Xcode now in public preview.md`
- `2025-10-15- Coding Agent for JetBrains IDE.md`
- `2025-09-03-New Features in GitHub Copilot in Eclipse.md`

### Step 2 — Build the YAML frontmatter

Always emit **all four** of these keys, in this order:

```yaml
---
title: "<Title>"          # Quote when it contains a colon, apostrophe, or special chars
labels: copilot           # REQUIRED — always present
date: <YYYY-MM-DD>        # Use the simple date form for new entries
type: new-releases        # or "improvements" — see decision rule below
private: true             # REQUIRED — always present
---
```

**`type` decision rule:**
- `new-releases` → a brand new feature, public preview, or GA announcement.
- `improvements` → a roll-up of enhancements / polish / bug fixes for an existing feature.

**`private` rule:** Always include `private: true`. Do not omit it, even if the user does not mention drafts.
**`labels` rule:** Always `labels: copilot`. Do not change or omit it.

### Step 3 — Scaffold the body

Use the canonical structure below (and the full template at [assets/changelog-template.md](./assets/changelog-template.md)):

```markdown
<!-- Optional poster image. Include only if the user mentions one or marks it as needed. -->
<img alt="<descriptive alt text>" src="<image url or //TODO:replace with poster.png>"/>

<Opening paragraph: 1–2 sentences. Convey excitement + the value proposition. Link the feature's docs page if known.>

## ✨ What's new

<Bullet points or short sub-sections describing the feature(s). Use bold sub-headers for each capability.>

## 🛠 Try it out

<Numbered setup steps. Include prerequisites (Copilot subscription) and IDE update instructions where relevant.>

## 💬 Share your feedback

Your feedback drives improvements. Let us know what you think using the in-product feedback option, or share your thoughts in the following channels:

<IDE-specific feedback links — see references/feedback-channels.md>
```

**Section rules:**
- Always include `## ✨ What's new` and `## 💬 Share your feedback`.
- Include `## 🛠 Try it out` whenever the feature has a setup or usage flow (almost always).
- Use the apostrophe character `'` (straight) to stay consistent with the newest entries; older files mix in curly `'` — prefer straight.
- **Feedback channels are driven entirely by the `product` answer** — not by what the title happens to mention. Use the snippet for the exact set of selected products from [references/feedback-channels.md](./references/feedback-channels.md).

### Feedback-channel selection (based on the `product` answer)

| Selected products | Pattern to use |
|---|---|
| `JetBrains` only | Single-IDE snippet → JetBrains issues repo |
| `Eclipse` only | Single-IDE snippet → Eclipse issues repo (one link only) |
| `Xcode` only | Single-IDE snippet → Xcode issues repo |
| Any 2 or all 3 selected | Multi-IDE bullet-list footer with one row per selected IDE, in the order JetBrains → Eclipse → Xcode |

**Eclipse rule (important):** Eclipse has exactly **one** canonical feedback channel: https://github.com/microsoft/copilot-for-eclipse/issues. Do **not** include the old `copilot-eclipse-feedback` issues link or the `community/discussions/151288` thread — they are deprecated.

Canonical multi-IDE block (omit rows for products the user did **not** select):

```markdown
Your feedback drives our roadmap. Let us know how this is working for you by using the feedback controls in your IDE or through your existing Copilot support channels.

- **In-product feedback**: Use the feedback options within your IDE.
- **GitHub discussions**: Share your thoughts in the following channels:
    - JetBrains [GitHub Copilot in JetBrains feedback repository](https://github.com/microsoft/copilot-jetbrains-feedback/issues)
    - Eclipse [GitHub Copilot for Eclipse issues](https://github.com/microsoft/copilot-for-eclipse/issues)
    - Xcode [GitHub Copilot in Xcode feedback repository](https://github.com/github/CopilotForXcode/issues)
```

### Step 4 — Create the file

Create the new `.md` file at the workspace root using the file-creation tool. Do **not** print the entire draft into chat — just confirm the path and offer a short summary of what was generated and what `//TODO:` placeholders the user should fill in.

## Quality Checklist (run before finishing)

- [ ] Filename matches `YYYY-MM-DD-<Title>.md` and lives at the workspace root.
- [ ] Frontmatter contains **all four required keys**: `title`, `labels: copilot`, `date`, `type`, **and** `private: true`.
- [ ] `labels` is exactly `copilot` and `private` is exactly `true` — never omitted.
- [ ] Title in frontmatter is quoted if it contains `:` or other YAML-sensitive characters.
- [ ] Body has the three required headings: `## ✨ What's new`, `## 🛠 Try it out` (when applicable), `## 💬 Share your feedback`.
- [ ] Feedback channels exactly match the **product(s)** the user selected — no extra IDEs, no missing ones.
- [ ] If multiple products were selected, the multi-IDE bullet-list footer is used (see [references/feedback-channels.md](./references/feedback-channels.md)).
- [ ] Image references either point to a real URL or are flagged with `//TODO:replace with <name>.png`.
- [ ] Tone matches existing entries: enthusiastic, developer-focused, value-prop first.

## Resources

- Template: [assets/changelog-template.md](./assets/changelog-template.md)
- Feedback URLs per IDE: [references/feedback-channels.md](./references/feedback-channels.md)
