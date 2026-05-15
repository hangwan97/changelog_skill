---
name: changelog
description: 'Draft a new GitHub Copilot IDE changelog entry (JetBrains, Eclipse, Xcode) for this repository. USE WHEN: the user asks to "create a changelog", "draft a release note", "write a new changelog entry", "announce a Copilot feature", or pastes raw release info that needs formatting. INTERVIEWS the user for target product(s) (JetBrains / Eclipse / Xcode — multi-select), release date, and the main content/notes; then DERIVES the title from the content and produces the correctly named file (YYYY-MM-DD-Title.md), YAML frontmatter, and a body scaffolded with the standard sections (poster image, ✨ What''s new, body, 💬 Share your feedback with product-specific channels). DO NOT USE for: editing unrelated docs, generating product code, or writing changelogs for non-Copilot products.'
argument-hint: '[release-date YYYY-MM-DD] [content]'
---

# Changelog Skill

Generates a new changelog entry for the GitHub Copilot IDE extensions (JetBrains, Eclipse, Xcode) following this repository's established conventions.

## Quick start (for contributors who just cloned this repo)

This skill exposes itself as a `/changelog` slash command in VS Code Copilot Chat.

In Copilot Chat, you can either:

- Type **`/changelog`** (with optional inline args), e.g.
  `/changelog 2026-05-20 Custom Agent for Xcode is now generally available...`, or
- Ask in plain English:
  > create a new changelog: Custom Agent for Xcode is now generally available, releasing 2026-05-20

Either way, you'll be asked three quick questions (target IDE(s), release date,
and content). The skill **derives the title and filename for you** and writes
the new file at the workspace root.

If `/changelog` does not appear, reload the VS Code window
(`Cmd/Ctrl+Shift+P` → **Developer: Reload Window**). Full install steps are
in [README.md](./README.md).

## When to Use

Trigger this skill whenever the user wants to:
- Create / draft / scaffold a new changelog entry or release note
- Announce a Copilot feature for JetBrains, Eclipse, or Xcode (public preview, GA, improvements)
- Convert raw notes / bullets into the canonical changelog format used in this repo

## Inputs to Collect

Use the `vscode_askQuestions` tool (one batched call) to gather the following from the user.
**Ask exactly these three questions — nothing more.** Do NOT ask the user for a title;
the skill derives the title from the content (see Step 1 below).

| # | Field | Required | Notes |
|---|-------|----------|-------|
| 1 | **Product(s)** | Yes | Which IDE(s) this changelog covers. **Multi-select** from: `JetBrains`, `Eclipse`, `Xcode`. Drives the feedback-channel block and helps shape the derived title. Ask with `multiSelect: true`. |
| 2 | **Release date** | Yes | `YYYY-MM-DD`. Default suggestion = today. |
| 3 | **Content** | Yes | Free-form: feature description, highlights, bullet points, raw release notes, links, screenshots, preview vs GA, etc. The skill uses this both to write the body **and** to derive the title. |

If the user supplied any of these in the slash-command arguments, pre-fill the
corresponding question and only ask for what is still missing.

### Question wording (suggested)

- `product` — *"Which product(s) does this changelog cover?"* with `multiSelect: true` and options `JetBrains`, `Eclipse`, `Xcode`.
- `release-date` — *"What is the release date? (YYYY-MM-DD)"*
- `content` — *"What's the changelog about? Paste raw notes, bullets, or a short description — I'll derive the title and structure the body."*

## Output Procedure

### Step 1 — Derive the title from the content

The user does **not** supply a title. Generate one from the **content** + **product(s)** answers.

Rules:
- **Title-case**, concise (target ~6–12 words).
- Mention the **feature/capability** and a **release-phase signal** when present in the content
  (e.g. "now in public preview", "is now generally available", "now GA", "new in GitHub Copilot in Eclipse").
- Mention the **selected IDE(s)** in the order JetBrains → Eclipse → Xcode. If all three were selected,
  use the form `for JetBrains, Eclipse, and Xcode`. For two, `for <A> and <B>`. For one, just `for <IDE>` or `in <IDE>`.
- Match the tone of existing entries in this repo. Examples to mirror:
  - `Custom Agent for JetBrains, Eclipse, and Xcode now in public preview`
  - `Coding Agent for JetBrains IDE`
  - `Agent Mode for JetBrains, Eclipse and Xcode is now generally available`
  - `New Features in GitHub Copilot in Eclipse`
- Do **not** include the date in the title.
- Do **not** wrap the title in quotes when generating it (it's quoted later in the YAML frontmatter).

Briefly show the derived title to the user (one line) before writing the file, so they can
override it if it's off. Don't re-prompt unless they push back.

### Step 2 — Derive the filename

Pattern: `YYYY-MM-DD-<Title>.md`

Rules (matching files already in this repo):
- Use the release date verbatim (e.g. `2025-11-18`).
- Keep the derived title's casing and spaces. Do **not** slugify.
- Strip characters that are illegal on macOS/Windows filesystems: `/ \ : * ? " < > |`. Replace `:` with nothing or a hyphen as needed.

Examples already in the repo:
- `2025-11-18- Custom Agent for JetBrains, Eclipse, and Xcode now in public preview.md`
- `2025-10-15- Coding Agent for JetBrains IDE.md`
- `2025-09-03-New Features in GitHub Copilot in Eclipse.md`

> Save the file at the **workspace root** (same level as the existing
> `YYYY-MM-DD-*.md` entries), not inside `.github/skills/changelog/`.

### Step 3 — Build the YAML frontmatter

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
  Infer this from the **content** answer (e.g. "performance improvements", "bug fixes",
  "smarter, faster" → `improvements`; "public preview", "now generally available",
  "introducing" → `new-releases`).

**`private` rule:** Always include `private: true`. Do not omit it, even if the user does not mention drafts.
**`labels` rule:** Always `labels: copilot`. Do not change or omit it.

### Step 4 — Scaffold the body

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

### Step 5 — Create the file

Create the new `.md` file at the workspace root using the file-creation tool. Do **not** print the entire draft into chat — just confirm the **derived title**, the file path, and offer a short summary of what was generated and what `//TODO:` placeholders the user should fill in.

## Quality Checklist (run before finishing)

- [ ] Title was **derived** by the skill (not asked from the user) and shown back for confirmation.
- [ ] Filename matches `YYYY-MM-DD-<Title>.md` and lives at the workspace root.
- [ ] Frontmatter contains **all five required keys**: `title`, `labels: copilot`, `date`, `type`, **and** `private: true`.
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
