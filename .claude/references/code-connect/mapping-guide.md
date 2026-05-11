# Code Connect — Mapping Guide

Reference for the `code-connect-mapper` agent. Defines how to discover, resolve,
and persist mappings between Figma components and code components.

## What is Code Connect

Code Connect links a Figma component node to its corresponding code component.
When configured, `get_design_context` and `get_code_connect_map` return the code
path alongside the Figma structure — enabling handoff specs to reference real imports.

## Mapping resolution strategy

### Step 1 — Read existing mappings

Call `get_code_connect_map` on the DS file key.
Returns: `{ nodeId: { path, componentName } }` for all already-mapped components.

### Step 2 — Discover unmapped components

Call `get_design_context` or `search_design_system` to list all published library components.
Compare against the map from Step 1. Build a list of unmapped component node IDs.

### Step 3 — Find candidate code components

For each unmapped Figma component, search the codebase for a matching component:
- Match by name: `DS/Button` → search for `Button`, `ButtonComponent`, `Btn`
- Match by directory convention: `src/components/Button/`, `src/ui/Button/`
- Look for export: `export default Button`, `export { Button }`

Use `Glob` and `Grep` to locate files:
```bash
# Find component files by name
glob: src/components/**/Button/index.tsx

# Find exports
grep: "export.*Button" in src/components/
```

### Step 4 — Generate suggestions

For each unmapped Figma component with a code candidate:
- Call `get_code_connect_suggestions` to let Figma validate and score the match.
- Review the suggestions — accept only high-confidence matches (score > 0.8 or unambiguous).

### Step 5 — Send confirmed mappings

For each confirmed match, build the mapping payload and call `send_code_connect_mappings`.

## Naming heuristics

| Figma component name | Likely code component |
|---|---|
| `DS/Button` | `Button`, `BaseButton` |
| `DS/Input/Default` | `Input`, `TextField`, `TextInput` |
| `DS/Badge` | `Badge`, `Tag`, `Chip` |
| `DS/Modal` | `Modal`, `Dialog` |
| `DS/Toast` | `Toast`, `Snackbar`, `Notification` |
| `DS/Avatar` | `Avatar`, `UserAvatar` |
| `DS/Skeleton` | `Skeleton`, `SkeletonLoader` |
| `DS/Navigation/Tabs` | `Tabs`, `TabBar`, `TabNavigation` |

## What NOT to map

- Local components (`[Local]`) — these are not in the DS and should not be mapped
- Detached instances — resolve the detachment before mapping
- Page-specific wrapper frames — only map reusable library components
- Internal sub-components (e.g. `DS/Button/_Icon`) — map only the public component

## Validation before sending

Before calling `send_code_connect_mappings`:
1. Verify the code file exists at the path using `Glob`.
2. Verify the component is exported from that file using `Grep`.
3. Verify the Figma node ID exists in the current file (from `get_code_connect_map` or `search_design_system`).

If any check fails, skip the mapping and flag it as "needs manual review".
