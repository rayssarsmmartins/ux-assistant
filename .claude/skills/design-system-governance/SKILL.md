---
name: design-system-governance
description: >
  Rules for auditing Figma files against the design system: component compliance,
  token compliance, library usage, and severity classification.
  Triggers: any task involving DS audit, compliance check, or token validation.
---

# Design System Governance Skill

## When to activate

Activate when the task involves:
- Auditing a Figma file or frame for DS compliance
- Identifying components created outside the library
- Detecting hard-coded values that should use tokens
- Generating a compliance score or report

## Compliance categories

### 1. Component compliance

A component is **compliant** when:
- It is an instance of a published library component (`DS/` prefix)
- The variant used exists in the library (not a detached or overridden variant)
- No local styles override the component's defined properties

A component is **non-compliant** when:
- It is a local component (`[Local]` — not published to the library)
- It is a detached instance (was a component, manually broken)
- It uses a variant that does not exist in the library
- It is a raw shape (rectangle, ellipse) replicating a DS component

**Severity:**
- Local or detached component replicating DS element → **Critical**
- Wrong variant used (DS component, but wrong variant) → **Warning**
- Raw shape that could be replaced by a DS component → **Warning**

### 2. Token compliance

A value is **compliant** when:
- It references a library variable (color token, spacing token, text style)
- The variable belongs to the published DS token collection

A value is **non-compliant** when:
- Fill uses a hard-coded hex color instead of a color token
- Spacing (padding, gap, margin) uses a raw pixel value instead of a spacing token
- Text style uses raw font-size/weight instead of a text token
- Border radius uses a raw value instead of a radius token

**Severity:**
- Hard-coded color on interactive or brand element → **Critical**
- Hard-coded color on neutral/structural element → **Warning**
- Hard-coded spacing or radius → **Warning**
- Hard-coded typography → **Warning**

### 3. Library freshness

A component is **outdated** when:
- It is an instance of a library component but from an older version
- The library has published updates not yet accepted in the file

**Severity:**
- Outdated component in a frame marked "Ready for dev" → **Warning**
- Outdated component in a draft frame → **Suggestion**

### 4. Naming compliance

Layer naming is **compliant** when:
- Frames follow `ScreenName/StateName` pattern (e.g. `Checkout/Default`)
- Components are named after their DS path (`DS/Button/Primary`)
- Groups are named semantically (not `Frame 42`, `Group 7`)

**Severity:**
- Unnamed layers in a frame marked "Ready for dev" → **Warning**
- Unnamed layers in a draft → **Suggestion**

## Compliance score calculation

```
score = (compliant_elements / total_elements) * 100

Elements counted:
- Each component instance (compliant vs non-compliant)
- Each token application (compliant vs hard-coded)
- Each named layer (compliant vs unnamed)
```

Score interpretation:
- 90–100%: Excellent — minor cleanup only
- 75–89%: Good — some issues to fix before handoff
- 50–74%: Needs work — significant rework required
- < 50%: Critical — do not hand off

## Audit scope

### Frame-level audit
- Analyze a single frame and all its children.
- Use `get_design_context` + `get_variable_defs` on the target node.

### File-level audit
- Iterate over top-level frames using `get_metadata` to list them.
- Run frame-level audit per frame, aggregate results.
- Flag frames with lowest scores first.

## When to suggest automatic correction

Suggest `use_figma` correction only when:
- The replacement is unambiguous (one DS component clearly maps to the non-compliant element)
- The user has approved the correction in the checkpoint
- Full seat is available

Never auto-correct without explicit user approval per batch of changes.
