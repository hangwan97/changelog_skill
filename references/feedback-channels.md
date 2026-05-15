# IDE Feedback Channels

Canonical URLs to use in the **`## 💬 Share your feedback`** section of every changelog. Pick only the rows that match the IDE(s) named in the announcement title.

## Standard URLs

| IDE | Channel | URL |
|-----|---------|-----|
| JetBrains | Issues | https://github.com/microsoft/copilot-intellij-feedback/issues |
| Eclipse | Issues | https://github.com/microsoft/copilot-for-eclipse/issues |
| Xcode | Issues | https://github.com/github/CopilotForXcode/issues |

## Recommended Snippets

### Single IDE — JetBrains
```markdown
Your feedback drives improvements. Let us know what you think using the in-product feedback option, or share your thoughts in the [GitHub Copilot for JetBrains feedback repository](https://github.com/microsoft/copilot-intellij-feedback/issues).
```

### Single IDE — Eclipse
```markdown
Your feedback drives improvements. Let us know what you think using the in-product feedback option, or share your thoughts in the [GitHub Copilot for Eclipse issues](https://github.com/microsoft/copilot-for-eclipse/issues).
```

### Single IDE — Xcode
```markdown
Your feedback drives improvements. Let us know what you think using the in-product feedback option, or share your thoughts in the [GitHub Copilot for Xcode feedback repository](https://github.com/github/CopilotForXcode/issues).
```

### Multi-IDE (JetBrains + Eclipse + Xcode)
```markdown
Your feedback drives our roadmap. Let us know how this is working for you by using the feedback controls in your IDE or through your existing Copilot support channels.

- **In-product feedback**: Use the feedback options within your IDE
- **GitHub discussions**: Share your thoughts in the following channels:
    - JetBrains [GitHub Copilot in JetBrains feedback repository](https://github.com/microsoft/copilot-intellij-feedback/issues)
    - Eclipse [GitHub Copilot for Eclipse issues](https://github.com/microsoft/copilot-for-eclipse/issues)
    - Xcode [GitHub Copilot in Xcode feedback repository](https://github.com/github/CopilotForXcode/issues)
```

## Selection Rules

1. **Use the `product` answer from the interview as the source of truth** — not the changelog title.
   The user explicitly selects one or more of `JetBrains`, `Eclipse`, `Xcode`.
2. For a JetBrains-only, Eclipse-only, or Xcode-only post, use the single-IDE snippet (one link, no bullet list).
3. **Eclipse has exactly one canonical channel**: https://github.com/microsoft/copilot-for-eclipse/issues. Do **not** add the old `copilot-eclipse-feedback` issues link or the `community/discussions/151288` thread — those are deprecated.
4. For 2 or 3 selected products, use the multi-IDE bullet-list snippet and **order rows JetBrains → Eclipse → Xcode**, including only the selected IDEs.
