# Claude Code Skills by Automate & Tweak

Reusable skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — Anthropic's CLI tool for software engineering with Claude.

## What Are Claude Code Skills?

Skills are markdown files that teach Claude Code how to perform specialized tasks. When you place a skill file in your project's `.claude/skills/` directory, it becomes available as a slash command (e.g., `/wcag`) that Claude Code can execute with full context of your codebase.

Skills are different from prompts or snippets — they define multi-step workflows that Claude follows autonomously, reading your actual source files and producing actionable results.

## Available Skills

| Skill | Description | Docs |
|-------|-------------|------|
| [`wcag`](./skills/wcag/) | WCAG 2.1 AA accessibility audit — traces render chains, checks semantic HTML, flags contrast issues, audits mobile elements, outputs prioritized report | [README](./skills/wcag/README.md) |

## Installation

1. Clone or download this repo
2. Copy the skill folder(s) you want into your project's `.claude/skills/` directory

```bash
# Example: install the WCAG skill
git clone https://github.com/automateandtweak/a-and-t-claude-skills.git
cp -r a-and-t-claude-skills/skills/wcag/ /path/to/your-project/.claude/skills/wcag/
```

Your project structure should look like:
```
your-project/
  .claude/
    skills/
      wcag/
        SKILL.md
        wcag-reference.md
  src/
  ...
```

## Usage

Once installed, invoke a skill in Claude Code using its slash command:

```
/wcag homepage
/wcag /about/
/wcag /products/123/
```

The WCAG skill will trace the page's render chain, run accessibility checks against the source code, and output a prioritized report with `file:line` references and fix suggestions.

## Contributing

Issues and pull requests are welcome. If you have a skill that could be useful to others, feel free to submit a PR.

When contributing a new skill:
- Place it in `skills/<skill-name>/`
- Include a `SKILL.md` (the skill definition) and a `README.md` (documentation)
- Keep skills framework-agnostic where possible
- Test the skill on at least one real project before submitting

## Author

Built by [Automate & Tweak](https://automateandtweak.com) — a multilingual directory platform for AI tools and automation solutions.

## License

[MIT](./LICENSE)
