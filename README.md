# changelog_skill

A GitHub Copilot **skill** + optional **slash command** that scaffolds new changelog entries
for the GitHub Copilot IDE extensions (JetBrains, Eclipse, Xcode), following a
consistent file-naming convention, YAML frontmatter, and body structure.

## What this gives you

- A guided interview (title, release date, target IDE(s), optional context) before any file is written.
- A correctly named file at your workspace root: `YYYY-MM-DD-<Title>.md`.
- Standard YAML frontmatter (`title`, `labels`, `date`, `type`, `private`).
- Body scaffolded with the standard sections:
  - `## ✨ What's new`
  - `## 🛠 Try it out`
  - `## 💬 Share your feedback` — feedback links auto-selected per IDE you targeted.

## Requirements

- **VS Code** with the **GitHub Copilot Chat** extension installed.
- For the optional slash command: VS Code setting `chat.promptFiles` set to `true` (default in recent releases).

## Install

In the **target repository** where you want to write changelogs, from the repo root:

### 1) Install the skill (required)

```bash
mkdir -p .github/skills
git clone https://github.com/<owner>/changelog_skill.git .github/skills/changelog_skill
```

That's it — the skill is now auto-discovered. Open Copilot Chat and ask in plain English:

> create a new changelog for Custom Agent for Xcode now GA, releasing 2026-05-20

Copilot will pick up `SKILL.md` (via its `description`) and run the interview.

### 2) Enable the `/changelog` slash command (optional)

```bash
mkdir -p .github/prompts
cp .github/skills/changelog_skill/prompts/changelog.prompt.md .github/prompts/
```

Reload the VS Code window (`Cmd/Ctrl+Shift+P` → **Developer: Reload Window**).
In Copilot Chat, type `/changelog` — auto-complete should show the command.

You can also pass arguments inline:

```
/changelog Custom Agent for Xcode now GA, releasing 2026-05-20
```

## Folder layout

```
changelog_skill/
├── SKILL.md                          # The skill definition (auto-loaded)
├── assets/
│   └── changelog-template.md         # Body scaffold reference
├── references/
│   └── feedback-channels.md          # Canonical IDE feedback URLs
├── prompts/
│   └── changelog.prompt.md           # Optional slash-command (copy to .github/prompts/)
├── README.md
└── LICENSE
```

After installing into a target repo:

```
<your-repo>/
└── .github/
    ├── skills/
    │   └── changelog_skill/          # cloned here
    └── prompts/
        └── changelog.prompt.md       # copied here (optional)
```

## How discovery works

| File | Triggered by | Discovery path |
|---|---|---|
| `SKILL.md` | Natural-language phrases matching its `description` field | `.github/skills/**/SKILL.md` |
| `*.prompt.md` | Explicit slash command (e.g. `/changelog`) | `.github/prompts/**/*.prompt.md` |

## Updating

```bash
cd .github/skills/changelog_skill
git pull
# If you also installed the slash command, refresh it:
cp prompts/changelog.prompt.md ../../prompts/
```

## Uninstall

```bash
rm -rf .github/skills/changelog_skill
rm -f .github/prompts/changelog.prompt.md
```

## License

[MIT](./LICENSE)
