# Design System Audit — Rules Reference

Detailed rules applied during audit. Agents load this file alongside the
`design-system-governance` SKILL.md to resolve edge cases.

## Component resolution rules

When `get_design_context` returns a component instance:
1. Check if the name starts with `DS/` → library component, likely compliant.
2. Check if the name starts with a known local prefix (file name, feature name) → local, flag as non-compliant.
3. If the instance is detached (no `componentId` in context) → flag as detached, Critical.
4. Use `search_design_system` to verify the component exists in the library if unsure.

## Token resolution rules

When `get_variable_defs` returns a value:
1. If the value has a variable binding → compliant, record the token name.
2. If the value is a raw hex → non-compliant, look up nearest token in `tokens.md`.
3. If the value is a raw pixel for spacing → non-compliant, map to nearest spacing token.
4. If the value has no variable binding but matches a known token value exactly → Warning
   (value happens to match, but binding is missing — still non-compliant).

## Edge cases

### Icons
- Icon components from an external icon library (e.g. Material Icons, Phosphor) are compliant
  if the library is officially adopted by the DS. Flag if from an unapproved library.

### Shadows and effects
- Box shadows using raw RGBA values → Warning (apply token if DS has shadow tokens).
- Blur effects without a DS token → Suggestion.

### Overrides
- Text overrides on library components (changing the label) → compliant, expected behavior.
- Color overrides on library components → Critical (breaks DS intent).
- Size overrides (width/height) that break the component's intended layout → Warning.

### Responsive frames
- Frames with constraints but no auto-layout → Suggestion (consider migrating to auto-layout).
- Mixed content with explicit pixel positions → Warning if the DS uses auto-layout components.

## Exclusions

Do not flag as non-compliant:
- Placeholder/lorem ipsum text in draft frames
- Annotation layers (typically on a separate "Annotations" page)
- Cover art or marketing frames (not product UI)
- Frames explicitly named `WIP`, `Archive`, or `[OLD]`
