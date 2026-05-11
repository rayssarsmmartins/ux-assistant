# Handoff Spec — Template

This template defines the structure of a handoff spec document.
Agents must follow this structure when composing the spec.
The output is serialized to JSON following `handoff-spec.schema.json`.

---

## Handoff — [Screen Name]

**Frame:** [Figma URL with node-id]
**File:** [Figma file name]
**Date:** [ISO date]
**Status:** Ready for dev | In review | Blocked

---

### Components

List every component instance visible in the frame.

| Component (Figma) | Variant | Code path | Notes |
|---|---|---|---|
| DS/Button | Primary | src/components/Button/index.tsx | — |
| DS/Input | Default | src/components/Input/index.tsx | — |
| DS/Badge | Promo | src/components/Badge/index.tsx | — |

For components without Code Connect: note "not mapped" in the Code path column.

---

### Tokens applied

List every design token applied in the frame, grouped by category.

**Color**
- [Element name]: [token-name] ([hex value])

**Spacing**
- [Element name / property]: [token-name] ([px value])

**Typography**
- [Element name]: [token-name] ([font / size / weight / line-height])

**Hard-coded values (non-compliant)**
- [Element name]: [value] — should be [token-name]

---

### States

List all states identified across sibling frames or component variants.

| State | Frame or source | Notes |
|---|---|---|
| Default | [URL] | Primary state |
| Loading | [URL] | Skeleton variant |
| Error | [URL] | Inline error message |
| Empty | [URL] | Zero-state |
| Disabled | Component variant | Via DS/Button disabled prop |

---

### Accessibility notes

Use severity levels: Critical / Warning / Suggestion.

**Critical**
- [Element]: [issue] → [recommended fix]

**Warning**
- [Element]: [issue] → [recommendation]

**Suggestion**
- [Element]: [observation]

---

### Open questions

List unresolved decisions that block or affect implementation.

- [Question] — owner: @[person] — raised by: @[person]
