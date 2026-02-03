# WCAG 2.1 AA Audit Skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that audits web pages for WCAG 2.1 AA accessibility issues by tracing the render chain and checking source code.

## What It Does

1. **Identifies the page** — finds the controller, view, and all includes/components from a page name or URL path
2. **Asks for external input** — optionally integrates WAVE results or known issues for cross-referencing
3. **Traces the render chain** — maps every file that contributes to the final HTML (templates, partials, CSS, JS)
4. **Runs structural checks** — verifies semantic HTML, heading hierarchy, ARIA attributes, labels, landmarks
5. **Flags contrast risks** — identifies color combinations and patterns likely to fail contrast ratios
6. **Audits mobile elements** — catches accessibility issues in mobile-only UI (hamburger menus, mobile filters, overlays)
7. **Outputs a prioritized report** — findings organized by severity (Critical / Major / Moderate) with `file:line` references and fix suggestions

## Prerequisites

**Recommended**: Install the [WAVE browser extension](https://wave.webaim.org/) for computed contrast checking. WAVE is also listed on [Automate & Tweak](https://automateandtweak.com/tool/wave-evaluation-tool/).

The skill performs source-level analysis. WAVE complements it by testing rendered output — together they catch issues neither can find alone.

## Installation

Copy the `skills/wcag/` folder into your project's `.claude/skills/` directory:

```bash
# From the repo root
cp -r skills/wcag/ /path/to/your-project/.claude/skills/wcag/
```

Your project should end up with:
```
your-project/
  .claude/
    skills/
      wcag/
        SKILL.md
        wcag-reference.md
```

## Usage

Invoke the skill in Claude Code with `/wcag` followed by a page name or URL path:

```
/wcag homepage
/wcag /about/
/wcag /products/123/
/wcag login
/wcag dashboard
```

The skill will:
1. Ask if you have WAVE results to include
2. Trace the full render chain for that page
3. Run all WCAG 2.1 AA checks against the source
4. Output a structured report with findings and fixes

## Companion Reference

The [`wcag-reference.md`](./wcag-reference.md) file is a companion reference document included with this skill. It provides:

- Safe color palette (verified WCAG AA contrast values)
- Known-failing colors to avoid
- Dark mode patterns and checklist
- Focus indicator standards
- Semantic HTML rules
- ARIA patterns
- Quick checklist for CSS/HTML work

Claude Code reads this file automatically when the skill references it.

## Customization

The skill is designed to work with any web stack (PHP, React, Vue, Django, Rails, static HTML, etc.). If your project uses a specific routing convention, you can add hints to Step 1 in `SKILL.md` to help the skill find pages faster.

For example, add your framework's route file location:
```markdown
- **Route configuration**: `config/routes.rb` (Rails) or `src/app/routes.tsx` (React Router)
```

## Author

Built by [Automate & Tweak](https://automateandtweak.com) — a multilingual directory platform for AI tools and automation solutions.

## License

[MIT](../../LICENSE)
