---
name: accessibility-review
description: >
  WCAG 2.1 AA checklist applied to Figma designs. Covers contrast ratios,
  touch targets, semantic hierarchy, focus states, and label completeness.
  Triggers: any task involving accessibility check, a11y review, or WCAG compliance.
---

# Accessibility Review Skill

## When to activate

Activate when the task involves:
- Reviewing a frame for WCAG 2.1 AA compliance
- Checking color contrast on specific elements
- Auditing interactive element sizes
- Generating accessibility notes for a handoff spec

## WCAG 2.1 AA criteria applied to Figma

### 1. Color contrast (SC 1.4.3 / 1.4.11)

**Text contrast — minimum ratios:**
| Text type | Minimum ratio |
|---|---|
| Normal text (< 18pt / < 14pt bold) | 4.5:1 |
| Large text (≥ 18pt / ≥ 14pt bold) | 3:1 |
| UI components and graphical objects | 3:1 |

**How to check in Figma context:**
- Get the foreground color token and background color token from `get_variable_defs`
- Look up their hex values in `tokens.md`
- Calculate contrast ratio: `(L1 + 0.05) / (L2 + 0.05)` where L = relative luminance
- Relative luminance: convert sRGB → linear, then `0.2126R + 0.7152G + 0.0722B`

**Known low-contrast token combinations to flag immediately:**
- `color.neutral.400` on white → ~2.6:1 (Critical)
- `color.neutral.500` on white → ~3.8:1 (fails for normal text, passes for large text)
- `color.neutral.600` on white → ~5.9:1 (passes AA)
- Any light color token on `color.neutral.0` with a ratio below 4.5:1

### 2. Touch targets (SC 2.5.5)

Minimum interactive area: **44×44px** (WCAG AA) / **24×24px** (WCAG AAA minimum, not recommended).

**What to check in `get_design_context`:**
- Buttons, links, checkboxes, radio buttons, toggles, icon buttons
- If the visible element is smaller than 44px, check if a transparent hit area layer exists
- Flag if neither the component nor any parent provides 44×44px

**Severity:** Critical if the only tappable area is below 44×44px.

### 3. Focus visibility (SC 2.4.7)

Every interactive element must have a visible focus state.

**How to check:**
- Look for sibling frames or component variants named `Focus`, `Focused`, `:focus`
- If none exist, flag as Warning — focus state not designed
- If a focus state exists, check that the focus indicator has a contrast ratio ≥ 3:1 against adjacent colors

### 4. Semantic hierarchy (SC 1.3.1)

Heading levels must not skip (H1 → H3 without H2 is non-compliant).

**How to infer from Figma:**
- Map text styles to heading levels by size: `text.heading.xl` = H1, `.lg` = H2, `.md` = H3, `.sm` = H4
- Check the visual order of text layers — heading hierarchy should descend, not jump
- Flag if a smaller heading token appears before a larger one in the reading order

### 5. Text alternatives (SC 1.1.1)

Images and icon-only elements must have text alternatives.

**What to flag:**
- Icon-only buttons with no visible label and no annotation for `aria-label`
- Decorative images that are not marked as decorative in the handoff
- Charts or data visualizations with no text description

**Severity:** Critical for interactive icons without labels; Suggestion for decorative images.

### 6. Error identification (SC 3.3.1)

Error messages must identify the field in error and describe the issue in text — not just with color.

**What to flag:**
- Error states that use only a red border or red icon with no explanatory text
- Error messages that say "Invalid" without describing what is wrong

## Severity assignment

| Criterion | Critical | Warning | Suggestion |
|---|---|---|---|
| Contrast | < 3:1 on any text | 3:1–4.5:1 on normal text | — |
| Touch target | < 44px no hit area | < 44px with unclear hit area | — |
| Focus | Not designed at all | Designed but low contrast | Minor visual inconsistency |
| Heading hierarchy | Skipped levels | — | Not using semantic tokens |
| Labels | Interactive icon no label | — | Decorative image not marked |
| Error states | Color only, no text | Text too generic | Text could be clearer |

## Output format

Always report using the format from `ux-communication` SKILL.md:

```
[Element name]: [current value / situation] → [standard violated] — [recommended fix]

Example:
"Label 'Email': color.neutral.400 (#9CA3AF) on white → contrast 2.6:1,
 WCAG AA requires 4.5:1. Fix: use color.neutral.700 (#595959)."
```
