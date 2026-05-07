# claude-skills-validator

Lint a Claude Skill `SKILL.md` file for spec compliance and triggering quality. Browser-only, single HTML file.

## Why

A SKILL.md is a contract between you and Claude. The `description` field decides whether the skill triggers; the body decides what Claude does once it triggers. Both fields have failure modes:

- **Frontmatter errors** (missing fields, malformed YAML) cause the skill to silently not load.
- **Vague descriptions** undertrigger ("help with various things") or overtrigger ("anything related to text").
- **Bloated bodies** burn context every time the skill loads.
- **Meta-language** in the body ("This skill helps you...") wastes tokens that should be instructions.

This validator catches these issues before you ship the skill. Each finding comes with a severity tag and a concrete fix.

## Use it

Open `index.html` in any browser. Or visit the hosted version at `https://0xelitesystem.github.io/claude-skills-validator/` once GitHub Pages is enabled.

1. Paste your SKILL.md into the textarea.
2. Click Validate (or Cmd/Ctrl+Enter from the textarea).
3. Read the report.

Click "Load example" to see the validator working against a known-good SKILL.md.

## What it checks

**Frontmatter (blocker-level):**
- Frontmatter delimiters present (`---` ... `---`)
- Required `name` field, slug-style identifier
- Required `description` field

**Description quality:**
- Length: minimum 80 chars (too-short descriptions trigger unreliably)
- Length: maximum 1500 chars (too-long descriptions waste context budget)
- Includes "when to use" phrasing (e.g. "Use when...", "Triggers on...")
- Has at least 2 quoted example trigger phrases
- Avoids vague language ("help with", "various", "general purpose")

**Body:**
- Not empty
- Not over 500 lines (move detail into bundled resource files)
- Has an H1 heading
- If body references `references/` or `scripts/`, description tells Claude to read them first
- No marketing meta-language ("This skill helps you...")

## Severity scale

- **blocker** — must fix; the skill won't load or won't trigger reliably
- **major** — should fix before shipping; will hurt triggering quality
- **minor** — improve when convenient
- **nit** — style preference

The verdict is `fail` if there is any blocker or 3 or more majors. Otherwise `pass`.

## What it doesn't check

- Whether your skill is *useful* (subjective)
- Whether your tools/scripts in `scripts/` work (run them yourself)
- Whether the skill conflicts with other skills in your install (only Claude can know)
- The actual triggering behavior (run a real prompt and see if Claude loads the skill)

This validator is a heuristic linter, not a definitive authority. It catches the patterns that most reliably break skills based on the [public Agent Skills specification](https://agentskills.io). If your skill passes the validator and still doesn't trigger, the description likely needs more specific trigger phrases for your use case.

## Pairs with

This tool is meant to be run against skills written from the templates in [claude-skills-templates](https://github.com/0xelitesystem/claude-skills-templates). The example skill loaded by the "Load example" button is a simplified version of one of those templates.

## Tech

- Single HTML file
- ~700 lines including CSS and JS
- Vanilla JS, no frameworks, no dependencies, no build step
- Tested in current Chrome, Firefox, Safari
- Light and dark themes, OS preference honored
- WCAG AA contrast on both themes

## License

MIT. See [LICENSE](LICENSE).

## Related

- [claude-skills-templates](https://github.com/0xelitesystem/claude-skills-templates) — five reference Skill patterns
- [prompt-templates](https://github.com/0xelitesystem/prompt-templates) — production prompts targeting LLM failure modes
- [claude-eval-harness](https://github.com/0xelitesystem/claude-eval-harness) — run a prompt against multiple Claude models
- [readme-slop-checker](https://github.com/0xelitesystem/readme-slop-checker) — audit a README for AI cliches
