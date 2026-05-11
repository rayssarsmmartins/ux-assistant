---
name: figma-context
description: >
  How to read, interpret, and navigate Figma files via MCP tools.
  Covers tool selection, frame URL parsing, layer interpretation,
  component resolution, and token extraction.
  Triggers: any task involving a Figma URL, frame inspection, or design context.
---

# Figma Context Skill

## When to activate

Activate when the task involves:
- A Figma URL (design file, FigJam, frame node link)
- Reading a frame's structure, components, or tokens
- Validating a design against the design system
- Generating documentation from a Figma frame

## Parsing a Figma URL

Extract the `file_key` and `node_id` from the URL before calling any tool.

```
https://figma.com/design/{FILE_KEY}/title?node-id={NODE_ID}
https://figma.com/board/{FILE_KEY}/title?node-id={NODE_ID}   ← FigJam
```

- `FILE_KEY`: alphanumeric string after `/design/` or `/board/`
- `NODE_ID`: value of `node-id` query param (e.g. `123-456`). If absent, targets the root document.

## Tool selection guide

### Reading a specific frame

1. Start with `get_metadata` if the file is large or the node count is unknown.
   - Use: lightweight scan of layer names, types, and IDs.
   - When: first time reading an unfamiliar file, or when you need to locate sub-frames.

2. Use `get_design_context` for the primary read.
   - Use: structured representation — components, variants, auto-layout, Code Connect, nested instances.
   - When: generating specs, auditing components, understanding hierarchy.
   - Note: defaults to React + Tailwind output. Specify stack if different.

3. Use `get_screenshot` to capture the visual state.
   - Use: image of the frame as rendered in Figma.
   - When: always pair with `get_design_context` so you have both structure and visual.

4. Use `get_variable_defs` for token inspection.
   - Use: returns all variables and styles applied to the selection — names, values, modes.
   - When: auditing token compliance, generating handoff docs with token names.

### Searching the design system

Use `search_design_system` to find components, styles, or variables by name.
- Pass a descriptive query: `"primary button"`, `"input field"`, `"error state badge"`.
- Returns matching items from all connected libraries.
- Always run this before inserting a component via `use_figma` to get the correct node ID.

### Reading FigJam diagrams

Use `get_figjam` for FigJam boards (URL contains `/board/`).
- Returns XML with node metadata and screenshots of each node.
- Interpret connector arrows as transitions, stickies as states or actions.

### Code Connect

Use `get_code_connect_map` to resolve which code component corresponds to a Figma node.
- Returns: `{ nodeId: { path, componentName } }`.
- Use in handoff docs to link each Figma component to the actual code import.

## Interpreting design context output

Key fields in `get_design_context` response:

- `components`: list of component instances with name, variant properties, and node ID
- `layout`: auto-layout settings (direction, gap, padding, alignment)
- `variables`: tokens applied to fills, strokes, spacing, typography
- `codeConnect`: mapped code components for instances that have Code Connect configured

## Layer naming conventions

Well-structured Figma files follow these conventions (flag if missing):

- Frames named after their screen/state: `Checkout/Default`, `Checkout/Loading`
- Components named after DS path: `DS/Button/Primary`
- Groups named semantically: `Header`, `ProductList`, `Footer/CTA`
- Unnamed layers (`Frame 123`, `Group 7`) indicate unfinished or migrated designs

## Handling large files

If `get_design_context` returns a response near the 20 KB limit:
1. Use `get_metadata` first to identify the specific node IDs you need.
2. Call `get_design_context` on individual sub-frames, not the root frame.
3. Summarize each sub-frame result before proceeding to the next.
