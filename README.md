# frontend-extract-design-system

A Claude Code skill that audits an existing Vue frontend project and formalizes its design system — scanning for hardcoded values, generating a W3C-format `design-tokens.json`, documenting component APIs, and checking interactive element consistency.

## What It Does

Starting from a Vue project that already has components and styles, this skill:

- Scans all `.vue` files and CSS token files
- Identifies hardcoded values that should be design tokens
- Generates `design-tokens.json` in W3C format
- Documents component props, slots, and emits
- Finds ad-hoc interactive elements (buttons, links) that bypass the shared component

It always shows a review list before writing any output files, so you can confirm scope before anything is generated.

## Install

Copy the skill into your local Claude Code skills directory:

```bash
cp -R frontend-extract-design-system ~/.claude/skills/
```

Restart Claude Code. The skill is available immediately.

## Usage

In a Claude Code session inside your Vue project:

```
使用 frontend-extract-design-system，審查這個專案的 design system
```

or in English:

```
Use frontend-extract-design-system to audit and extract the design system from this project
```

## Workflow

```
1. Locate project structure (tokens file, global styles, components)
         ↓
2. Read foundations — color, spacing, radius, typography, shadow, transition
         ↓
3. Scan every .vue file
         ↓
4. Classify findings:
   - 要補齊 / To Formalize  (hardcoded values → tokens)
   - 要整理 / To Document   (components needing API docs)
   - 不需要抽取 / To Skip   (page-specific, one-off styles)
         ↓
   ← wait for confirmation →
         ↓
5. Generate outputs (see below)
         ↓
6. Validate consistency across all outputs
```

## Output Files

| File | Path | Contents |
|------|------|----------|
| `design-tokens.json` | `src/design-system/design-tokens.json` | W3C-format tokens with `$value` and `$type` |
| `token-audit.md` | `src/design-system/token-audit.md` | Per-component list of hardcoded values and suggested tokens |
| `components.md` | `src/design-system/components.md` | Props, slots, and emits for atoms and molecules |
| `component-reuse-audit.md` | `src/design-system/component-reuse-audit.md` | Ad-hoc interactive elements that bypass the shared button component |

### design-tokens.json format

```json
{
  "color": {
    "neutral": {
      "0":   { "$value": "#FFFFFF", "$type": "color" },
      "900": { "$value": "#3B3B3B", "$type": "color" }
    }
  },
  "spacing": {
    "xs": { "$value": "8px",  "$type": "dimension" },
    "m":  { "$value": "16px", "$type": "dimension" },
    "l":  { "$value": "24px", "$type": "dimension" }
  },
  "borderRadius": {
    "xs":    { "$value": "8px",    "$type": "dimension" },
    "round": { "$value": "1000px", "$type": "dimension" }
  },
  "transition": {
    "base": { "$value": "200ms ease", "$type": "duration" }
  }
}
```

## Hardcoded Value Rules

**Flagged as non-compliant:**
- Raw hex colors: `#6C0820`
- Raw px spacing: `8px`, `16px`, `24px`
- Raw px radius: `4px`, `8px`
- Transition values: `300ms ease`
- Inline font sizes not covered by global typography classes

**Acceptable as-is (not flagged):**
- Percentages: `100%`, `aspect-ratio: 5/2`
- Zero values: `margin: 0`
- Border widths: `1px`, `1.5px`
- Values inside `clamp()` — intentional responsive sizes
- SVG geometry attributes: `viewBox`, `width`/`height` on inline SVGs

## Scope

**Included in audit:**
- All `.vue` files under `src/components/`
- Global styles (`global.css`, `tokens.css`, or equivalent)
- Inline `<style>` blocks in views that define reusable patterns

**Excluded from audit:**
- Third-party component libraries
- Generated files
- Test files
- Animation keyframes (unless they use non-token timing values)

## Requirements

- Claude Code (latest)
- Vue 3 project
- No Figma MCP required — runs entirely from local source files

## Related

[figma-ds-workflows](https://github.com/chelswcs/figma-ds-workflows) — companion skills for extracting and auditing design systems directly in Figma.

## License

MIT
