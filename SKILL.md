---
name: frontend-extract-design-system
description: Extract and formalize a reusable design system from an existing Vue frontend project. Use when the task is to audit existing components for token compliance, generate design-tokens.json from CSS variables, document component APIs, find ad-hoc button/interactive elements that bypass the shared component, and produce a design system summary. Trigger this skill whenever the user wants to: extract or formalize a design system from frontend code, check if components are using design tokens correctly, generate token JSON output, document Vue component props and slots, audit hardcoded values in CSS, ensure button/interactive styling is consistent across pages, find places where button styles are duplicated or inconsistent, or organize their component library. Also trigger when the user says things like "整理 design system", "抽取元件", "token 輸出", "audit 元件", "component 文件", "button 規範", "hover 一致", "樣式不統一".
---

# Frontend Extract Design System

Use this skill when the goal is to analyze an existing Vue project and produce a well-documented, reusable design system from it.

Do not start by writing output files. Start by reading and understanding the codebase.

This skill covers the full extraction workflow:
- scan project structure (tokens, global styles, components)
- classify findings by layer (foundations, atoms, molecules, organisms)
- propose what to formalize, document, and skip
- execute: generate `design-tokens.json`, audit token compliance, document component APIs
- validate output consistency

## Step 0: Locate project structure

Before anything else, map the project:

1. Find the tokens file (`tokens.css`, `variables.css`, or similar)
2. Find the global styles file
3. List all components under `src/components/` (or equivalent)
4. Note the component organization pattern (atomic design, feature-based, flat, etc.)

If no tokens file exists, the project needs a foundation-first approach — document all unique hardcoded values before extracting components.

## Step 1: Read foundations

Read the tokens/variables file carefully. Categorize every token by group:

- **Color** — palettes and their scale steps
- **Spacing** — spacing scale values
- **Radius** — border-radius scale
- **Typography** — font families, sizes, weights
- **Shadow** — box-shadow definitions
- **Transition** — animation timing
- **Layout** — container widths, padding, breakpoints

Note any obvious gaps — for instance, a spacing scale that jumps from `8px` to `16px` with no `12px`, or font sizes used in components that have no corresponding token.

## Step 2: Scan all components

Read every `.vue` file. For each component, record:

1. **Layer** — atom / molecule / organism (based on directory or composition depth)
2. **Props** — name, type, default, required
3. **Slots** — names and purpose
4. **Emits** — event names and payload types
5. **Token usage** — which CSS variables it references
6. **Hardcoded values** — any raw px values, hex colors, or unitless numbers in `<style>`
7. **Typography classes** — does it use global text classes (`.text-h2`, `.text-small`) or define its own font styles inline?
8. **Ad-hoc interactive elements** — any `<button>`, `<a>` with `cursor: pointer`, or element with `cursor: pointer` in CSS that is NOT using the project's shared button component (e.g. `AppButton`). These are the main cause of inconsistent hover/active states across pages.

## Step 3: Classify and propose

Sort your findings into three lists. Always show these to the user before writing any output files.

### 要補齊 / To Formalize
Hardcoded values that should become tokens. Group by type:
- colors that appear more than once across components
- spacing values not in the scale
- font sizes defined inline without a token

### 要整理 / To Document
Components that are usable but lack clear API documentation. Prioritize atoms and molecules over organisms.

### 不需要抽取 / To Skip
- Page-specific layout wrappers
- One-off decorative styles tied to a single screen
- Inline overrides inside organism components that won't be reused

Present this review to the user. Wait for confirmation before proceeding to Step 4.

## Step 4: Execute

Run all three outputs in order. Do not skip any.

### Output A — design-tokens.json

Generate a `design-tokens.json` file using the W3C Design Token Community Group format.

Each token must have:
- `$value` — the resolved value (not a CSS variable reference)
- `$type` — one of: `color`, `dimension`, `fontFamily`, `fontWeight`, `duration`, `shadow`, `number`

Group tokens by category using nested objects. See `references/output-templates.md` for the exact format.

Place the file at: `src/design-system/design-tokens.json`

### Output B — Token compliance audit

For each component with hardcoded values, produce an audit entry:

```
## ComponentName (path/to/Component.vue)

### Hardcoded values found
| Location | Value | Suggested token |
|----------|-------|-----------------|
| .element { gap: 5px } | 5px | var(--spacing-tiny) |
| .element { margin-top: 8px } | 8px | var(--spacing-xs) |

### Missing tokens (no existing token matches)
- padding: 6px 20px → suggest adding --spacing-xxs-pill or using --spacing-xxs / --spacing-xl
```

Also note whether the component uses global typography classes or defines its own font styles — the latter should be migrated to global classes where possible.

Save to: `src/design-system/token-audit.md`

### Output C — Component API docs

For each atom and molecule, generate a documentation entry. See `references/output-templates.md` for the template.

Organisms: document at a high level (purpose, slot regions) without full prop tables — they are too context-specific to document exhaustively.

Save to: `src/design-system/components.md`

### Output D — Component reuse audit (button consistency)

The goal of this output is to ensure interactive elements are consistent — same hover/active behavior, same visual language — across all pages.

For every ad-hoc interactive element found in Step 2 (item 8), produce an entry:

```
## Ad-hoc button: .class-name (path/to/Component.vue)

**Element:** <a> / <button> / <div>
**Problem:** Styles hover/active independently instead of using AppButton
**Current hover:** background changes to var(--lake-blue-600)
**AppButton equivalent:** variant="secondary" | or propose a new variant

### Recommended action
Option A — Replace with <AppButton variant="secondary">
Option B — Add variant="link-sm" to AppButton with these styles: ...
```

Group findings by severity:
- **High** — interactive element with no consistent hover state at all
- **Medium** — element with hover but different visual language than AppButton
- **Low** — element that mostly matches AppButton but has minor hardcoded overrides

Save to: `src/design-system/component-reuse-audit.md`

## Step 5: Validate

After all three outputs are written, run a consistency check:

- Every color referenced in `token-audit.md` as "missing" → does it now appear in `design-tokens.json`? If not, flag it.
- Every component listed in `components.md` → does it actually exist at the noted path?
- `design-tokens.json` → are all values resolved (no `var()` references inside `$value`)?

Report the validation results to the user as a short summary.

## Scope rules

**Include in audit:**
- All `.vue` files under `src/components/`
- Global styles (`global.css`, `tokens.css`)
- Any inline `<style>` blocks in views that define reusable patterns

**Exclude from audit:**
- Third-party component libraries
- Generated files
- Test files
- Animation keyframes (unless they use non-token timing values)

## Token compliance rules

A value is non-compliant (hardcoded) when it appears directly in a style rule and a matching token exists or should exist:

- Raw hex colors: `#6C0820` → check against token palette
- Raw px spacing: `8px`, `16px`, `24px` → check against spacing scale
- Raw px radius: `4px`, `8px` → check against radius scale
- Transition values: `300ms ease` → check against transition tokens
- Raw font-size values not covered by global typography classes

A value is acceptable as hardcoded when:
- It is a percentage: `100%`, `aspect-ratio: 5/2`
- It is zero: `margin: 0`
- It is a border width: `1px`, `1.5px` (too small to tokenize)
- It is inside a `clamp()` — these are intentional responsive sizes
- It is used for SVG geometry: `viewBox`, `width`/`height` on inline SVGs

Use references/output-templates.md for the exact JSON and markdown output formats.
