---
agent: agent
description: Draft a new GitHub Copilot IDE changelog entry (JetBrains, Eclipse, Xcode) using the changelog_skill.
---

# /changelog — New Copilot IDE Changelog Entry

Use the `changelog_skill` to scaffold a new changelog entry for this repository.

> **Install location:** copy this file to `.github/prompts/changelog.prompt.md`
> in the repo where you want the slash command to be available.
> The relative paths below assume the skill lives at `.github/skills/changelog_skill/`.

## Inputs (from this invocation)

The user invoked the slash command `/changelog` and may have included free-text after it
(e.g. `/changelog Custom Agent for Xcode now GA, releasing 2026-05-20`).
Treat any text after the command as a draft of the **title** and/or **release date** —
parse what you can, and ask only for what is still missing.

## Procedure

Follow the skill's procedure exactly:

1. Read the full skill instructions from
   [.github/skills/changelog_skill/SKILL.md](../skills/changelog_skill/SKILL.md).
   Also load the supporting references when relevant:
   - [.github/skills/changelog_skill/assets/changelog-template.md](../skills/changelog_skill/assets/changelog-template.md)
   - [.github/skills/changelog_skill/references/feedback-channels.md](../skills/changelog_skill/references/feedback-channels.md)

2. Interview the user with `vscode_askQuestions` (one batched call) for any **missing** inputs:
   - **Title** (required)
   - **Release date** in `YYYY-MM-DD` (required, default = today)
   - **Product(s)** — multi-select from `JetBrains`, `Eclipse`, `Xcode` (required)
   - **Main context / highlights** (optional)

   Pre-fill any answers that were already supplied in the slash-command arguments
   so the user only confirms what is left.

3. Generate the file at the **workspace root** using the naming pattern
   `YYYY-MM-DD-<Title>.md` (keep the title's original casing and spaces;
   strip filesystem-illegal characters `/ \ : * ? " < > |`).

4. Emit the required YAML frontmatter — **all five keys, in this order**:
   `title`, `labels: copilot`, `date`, `type` (`new-releases` or `improvements`), `private: true`.
   Quote the title if it contains `:` or other YAML-sensitive characters.

5. Scaffold the body with the standard sections:
   - `## ✨ What's new`
   - `## 🛠 Try it out` (when there is a setup or usage flow — almost always)
   - `## 💬 Share your feedback`

6. Pick feedback channels based **only** on the products the user selected.
   Use the canonical Eclipse link `https://github.com/microsoft/copilot-for-eclipse/issues`.
   For 2+ products, use the multi-IDE bullet-list footer
   (order: JetBrains → Eclipse → Xcode), including only selected IDEs.

7. Run the skill's quality checklist before finishing.

8. Confirm the created file path and list any `//TODO:` placeholders the user
   should fill in. **Do not** dump the full draft into chat.
