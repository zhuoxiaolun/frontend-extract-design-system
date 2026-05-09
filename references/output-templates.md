# Output Templates

## design-tokens.json (W3C format)

```json
{
  "color": {
    "neutral": {
      "0":    { "$value": "#FFFFFF", "$type": "color" },
      "50":   { "$value": "#F4F4F4", "$type": "color" },
      "100":  { "$value": "#DBDBDB", "$type": "color" },
      "900":  { "$value": "#3B3B3B", "$type": "color" },
      "1000": { "$value": "#000000", "$type": "color" }
    },
    "cherry": {
      "500": { "$value": "#F4ADBD", "$type": "color" },
      "600": { "$value": "#DE9DAC", "$type": "color" }
    }
  },
  "spacing": {
    "tiny": { "$value": "2px",  "$type": "dimension" },
    "xxs":  { "$value": "4px",  "$type": "dimension" },
    "xs":   { "$value": "8px",  "$type": "dimension" },
    "s":    { "$value": "12px", "$type": "dimension" },
    "m":    { "$value": "16px", "$type": "dimension" },
    "l":    { "$value": "24px", "$type": "dimension" },
    "xl":   { "$value": "32px", "$type": "dimension" }
  },
  "borderRadius": {
    "xxs":   { "$value": "4px",    "$type": "dimension" },
    "xs":    { "$value": "8px",    "$type": "dimension" },
    "round": { "$value": "1000px", "$type": "dimension" }
  },
  "fontFamily": {
    "sansZh":  { "$value": "\"Noto Sans TC\", sans-serif", "$type": "fontFamily" },
    "serifEn": { "$value": "\"Cormorant\", serif",         "$type": "fontFamily" }
  },
  "shadow": {
    "card":  { "$value": "0 2px 12px rgba(0,0,0,0.06)",  "$type": "shadow" },
    "hover": { "$value": "0 6px 24px rgba(0,0,0,0.10)", "$type": "shadow" }
  },
  "transition": {
    "base": { "$value": "200ms ease", "$type": "duration" }
  },
  "layout": {
    "containerMax":     { "$value": "1200px", "$type": "dimension" },
    "containerPadding": { "$value": "24px",   "$type": "dimension" }
  }
}
```

---

## Component API entry (components.md)

### Atoms and Molecules

```markdown
## ComponentName

**Path:** `src/components/atoms/ComponentName.vue`
**Layer:** Atom | Molecule
**Purpose:** One sentence description of what this component does.

### Props

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| variant | `'primary' \| 'secondary' \| 'ghost'` | `'primary'` | No | Visual style |
| to | `string` | — | No | If provided, renders as RouterLink |
| full | `boolean` | `false` | No | Stretches to full width |

### Slots

| Slot | Description |
|------|-------------|
| default | Button label or content |

### Emits

None / or list events.

### Token usage

Uses: `--cherry-500`, `--cherry-600`, `--cherry-700`, `--lake-blue-*`, `--spacing-xs`, `--spacing-s`, `--spacing-l`, `--radius-round`, `--font-sans-zh`, `--transition-base`

### Usage example

\`\`\`vue
<AppButton variant="primary">送出</AppButton>
<AppButton variant="secondary" to="/projects">查看作品</AppButton>
\`\`\`
```

### Organisms (high-level only)

```markdown
## OrganismName

**Path:** `src/components/organisms/OrganismName.vue`
**Layer:** Organism
**Purpose:** One sentence description.

### Slot regions

| Slot | Purpose |
|------|---------|
| default | Main content area |
| header | Optional header override |

### Notes

- Any important behavioral notes
- Dependencies on other components
```

---

## token-audit.md structure

```markdown
# Token Compliance Audit
Generated: YYYY-MM-DD

## Summary
- Total components scanned: N
- Components fully compliant: N
- Components with hardcoded values: N
- Missing tokens (not in tokens.css): N

---

## ComponentName (src/components/layer/ComponentName.vue)

### Status: ⚠️ Needs attention | ✅ Compliant

### Hardcoded values found
| Selector | Property | Value | Suggested token |
|----------|----------|-------|-----------------|
| `.element` | `gap` | `5px` | `var(--spacing-tiny)` |
| `.element` | `margin-top` | `8px` | `var(--spacing-xs)` |
| `.element` | `padding` | `6px 20px` | No exact match — see below |

### Missing tokens (value has no matching token)
- `padding: 6px` — between `--spacing-xxs (4px)` and `--spacing-xs (8px)`. Recommend using `--spacing-xxs` or adding a new `--spacing-xxs-plus: 6px` token.

### Typography
- [ ] Uses global typography classes (`.text-*`)
- [x] Defines font styles inline → migrate to `.text-small` or `.text-caption`
```
