# Design System — Component Inventory

Reference file for the component library. Used by agents to resolve correct component names
before writing to Figma canvas or generating handoff documentation.

## How to use

Load this file when a task involves inserting, replacing, or documenting components.
Cross-reference with `search_design_system` MCP tool for live lookup.

## Component naming convention

Figma components follow the pattern: `DS/<Category>/<Name>` with variants as properties.

Example:
- `DS/Button` — variants: `Primary`, `Secondary`, `Ghost`, `Danger`
- `DS/Input` — variants: `Default`, `Masked`, `Search`, `TextArea`
- `DS/Badge` — variants: `Neutral`, `Promo`, `Success`, `Warning`, `Error`

## Categories

Fill in with the actual component categories and names from your design system.

```
DS/Button
DS/Input
DS/Badge
DS/Card
DS/Modal
DS/Toast
DS/Navigation
DS/Icon
DS/Avatar
DS/Skeleton
DS/Divider
DS/Tooltip
```

## Notes

- Components marked with `[Library]` come from a published shared library.
- Components marked with `[Local]` are file-specific and not published.
- Always prefer `[Library]` components. Flag `[Local]` components in audit reports.
