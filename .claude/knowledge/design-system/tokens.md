# Design System — Token Reference

Reference file for design tokens. Used by agents to map Figma variable names to their
semantic meaning and validate token usage in audits and handoff specs.

## How to use

Load this file when a task involves checking token compliance, generating handoff docs
with token names, or identifying hard-coded values that should use tokens.

## Token categories

### Color

Tokens follow the pattern: `color.<category>.<scale>` or `color.<category>.<role>`

```
color.neutral.0        → #FFFFFF (background default)
color.neutral.100      → light gray
color.neutral.500      → mid gray (caution: often fails contrast on white)
color.neutral.700      → #595959 (minimum for body text on white — AA)
color.neutral.900      → #1A1A1A (headings)
color.primary.default  → brand primary action
color.feedback.success.default
color.feedback.warning.default
color.feedback.error.default
```

### Spacing

Tokens follow the pattern: `spacing.<scale>` where scale maps to multiples of 4px.

```
spacing.1  →  4px
spacing.2  →  8px
spacing.3  → 12px
spacing.4  → 16px
spacing.6  → 24px
spacing.8  → 32px
spacing.10 → 40px
spacing.12 → 48px
```

### Typography

Tokens follow the pattern: `text.<role>.<size>`

```
text.heading.xl   → Inter 32/40 weight 700
text.heading.lg   → Inter 24/32 weight 700
text.heading.md   → Inter 20/28 weight 600
text.heading.sm   → Inter 16/24 weight 600
text.body.lg      → Inter 16/24 weight 400
text.body.md      → Inter 14/20 weight 400
text.body.sm      → Inter 12/16 weight 400
text.label.md     → Inter 14/20 weight 500
text.label.sm     → Inter 12/16 weight 500
```

### Border radius

```
radius.sm  →  4px
radius.md  →  8px
radius.lg  → 12px
radius.xl  → 16px
radius.full → 9999px (pills, avatars)
```

## Compliance rules

- Hard-coded hex values are non-compliant — always map to a token.
- Hard-coded pixel values for spacing are non-compliant if a token exists for that value.
- Typography using raw font-size/weight instead of a text token is non-compliant.
