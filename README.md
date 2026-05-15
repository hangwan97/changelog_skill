# changelog_skill

A GitHub Copilot skill that scaffolds new changelog entries for the GitHub Copilot IDE
extensions (JetBrains, Eclipse, Xcode) — with a `/changelog` slash command in
VS Code Copilot Chat.

## Install

### 1. Open your changelog repo

```bash
cd path/to/your/changelog-repo   # or: mkdir my-changelogs && cd my-changelogs && git init
```

### 2. Install the skill + slash command

Copy-paste this one block:

```bash
mkdir -p .github/skills .github/prompts
git clone https://github.com/hangwan97/changelog_skill.git .github/skills/changelog_skill
cp .github/skills/changelog_skill/template/changelog.prompt.md.tmpl .github/prompts/changelog.prompt.md
```

That's it. Reload the VS Code window
(`Cmd/Ctrl+Shift+P` → **Developer: Reload Window**) and you're ready to go.

## Use

In Copilot Chat, either:

- Type **`/changelog`** (with optional inline args), e.g.
  ```
  /changelog Custom Agent for Xcode now GA, releasing 2026-05-20
  ```
- Or just ask in plain English:
  > create a new changelog for *Custom Agent for Xcode now GA, releasing 2026-05-20*

You'll be asked a few quick questions (title, date, target IDE(s), optional context),
then the new file is written at your repo root: `YYYY-MM-DD-<Title>.md`.

## Requirements

- VS Code with the **GitHub Copilot Chat** extension.
- Setting `chat.promptFiles` set to `true` (default in recent VS Code releases).

## What's inside the skill

| File | Purpose |
|---|---|
| `SKILL.md` | The skill definition — auto-loaded by Copilot |
| `template/changelog.prompt.md.tmpl` | Source for the `/changelog` slash command (copied to `.github/prompts/` during install) |
| `assets/changelog-template.md` | Body scaffold reference |
| `references/feedback-channels.md` | Canonical IDE feedback URLs |

> The slash-command source is shipped as a `.tmpl` file (not `.prompt.md`) so it
> isn't auto-discovered by VS Code from inside the skill folder. The install step
> above copies it to `.github/prompts/changelog.prompt.md`, which is the only
> place that becomes the actual `/changelog` command.

## Update

```bash
cd .github/skills/changelog_skill && git pull
cp template/changelog.prompt.md.tmpl ../../prompts/changelog.prompt.md
```

## Uninstall

```bash
rm -rf .github/skills/changelog_skill .github/prompts/changelog.prompt.md
```

## License

[MIT](./LICENSE)
