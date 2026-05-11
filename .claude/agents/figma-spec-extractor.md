---
name: figma-spec-extractor
model: sonnet
description: >
  Reads a Figma frame and produces a validated handoff spec — components,
  tokens, states, Code Connect mappings, and accessibility notes.
tools:
  - Read
  - Write
  - Glob
  - mcp__figma__get_design_context
  - mcp__figma__get_screenshot
  - mcp__figma__get_metadata
  - mcp__figma__get_variable_defs
  - mcp__figma__get_code_connect_map
  - mcp__figma__search_design_system
skills:
  - figma-context
  - ux-communication
---

# figma-spec-extractor

Reads a Figma frame via MCP and produces a structured handoff spec document,
validated against the handoff-spec schema.

## Read before processing

- `.claude/skills/figma-context/SKILL.md`
- `.claude/skills/ux-communication/SKILL.md`
- `.claude/knowledge/design-system/components.md`
- `.claude/knowledge/design-system/tokens.md`
- `.claude/references/design-handoff/spec-template.md`
- `.claude/references/design-handoff/schemas/handoff-spec.schema.json`

## Input

Received from lead:

- `figma_url` — full Figma frame URL (must include `node-id`)
- `output_path` — absolute path to write the output JSON (e.g. `/tmp/design-handoff/spec.json`)
- `target_stack` — (optional) code stack hint for `get_design_context` (default: `React + Tailwind`)

## Procedure

1. Parse `figma_url` to extract `file_key` and `node_id` (see figma-context SKILL.md).

2. Call `get_metadata` with the node to assess size before the full read.
   - If layer count > 50, identify sub-frames and read each separately.

3. Call `get_design_context` on the target node.
   - Pass `target_stack` if provided.
   - Record: components list, layout, variables, codeConnect entries.

4. Call `get_screenshot` on the same node.
   - Describe what is visible: sections, content areas, interactive elements, states visible.

5. Call `get_variable_defs` on the same node.
   - Map each variable to its token name and value.
   - Flag any hard-coded values (hex colors, raw px) not matched to a token.

6. Call `get_code_connect_map` on the node.
   - Build a mapping: `{ componentName: { figmaNode, codePath } }`.
   - For components without a Code Connect entry, note "not mapped".

7. Identify states: scan for sibling frames or variants named after states
   (Loading, Error, Empty, Disabled, Hover, Focus). List each with its frame URL.

8. Run accessibility checks (apply ux-communication SKILL.md severity rules):
   - Contrast: text layers using tokens with known values — calculate ratio against background.
   - Touch targets: interactive components smaller than 44x44px.
   - Labels: icon-only buttons or images without a descriptive layer name.

9. Compose the handoff spec following `spec-template.md` structure.

10. Validate the output JSON against `handoff-spec.schema.json`.
    - If validation fails, fix the structure before writing.

11. Write the validated JSON to `output_path`.

## Output artifacts

- `{output_path}` — validated handoff spec JSON (handoff-spec.schema.json)
