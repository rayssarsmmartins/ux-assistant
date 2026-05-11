---
name: figjam-to-stories
model: sonnet
description: >
  Reads a FigJam flow diagram and converts it into user stories with EARS-style
  acceptance criteria, grouped by happy path, alternatives, and error states.
tools:
  - Read
  - Write
  - mcp__figma__get_figjam
  - mcp__figma__get_screenshot
skills:
  - ux-communication
---

# figjam-to-stories

Reads a FigJam diagram via MCP, interprets the flow structure, and produces
a validated user-stories JSON ready for Jira or Notion publication.

## Read before processing

- `.claude/skills/ux-communication/SKILL.md`
- `.claude/references/flow-to-stories/schemas/user-stories.schema.json`

## Input

Received from lead:

- `figjam_url` — full FigJam board URL (must be `/board/` URL, not `/design/`)
- `epic_name` — name of the epic or feature this flow belongs to
- `output_path` — absolute path to write the output JSON

## Procedure

### 1. Read the diagram

Call `get_figjam` on the board URL.
- Parse the XML response: extract all nodes (stickies, shapes, text boxes) and connectors.
- Build a graph: `{ nodeId: { label, type, outgoing_connections: [nodeId] } }`.

Call `get_screenshot` on the board.
- Use the screenshot to validate the graph structure visually and identify any nodes
  whose labels are unclear from the XML alone.

### 2. Identify structural elements

Map FigJam elements to flow concepts:
- **Rectangles / rounded rectangles**: screens or steps
- **Diamonds / decision shapes**: branching points (if/else, condition)
- **Stickies**: annotations, open questions, actor labels
- **Connectors with labels**: transitions with conditions (e.g. "CEP válido", "Erro 400")
- **Swim lanes**: actor separation (if present)

### 3. Identify actors

Look for sticky notes or labels near the start node that name the actor.
If no explicit actor is found, infer from the flow name or `epic_name`.
List all actors in the output `actors` field.

### 4. Trace paths

Walk the graph from the start node:
- **Happy path**: the primary sequence of steps with no branching or error conditions.
- **Alternative paths**: branches triggered by conditions that still lead to success.
- **Error paths**: branches that lead to an error state, empty state, or dead end.

Each path becomes one or more user stories.

### 5. Write user stories

For each meaningful step or branch, generate a user story:

```
As [actor], I want to [action], so that [benefit].
```

Rules:
- One story per major screen transition or decision point — not per micro-interaction.
- Group acceptance criteria by path: happy, alternative, error.
- Each criterion follows Given/When/Then format.
- Record the originating `figjam_node_id` for traceability.
- Flag ambiguous branches or missing states as `open_questions` on the relevant story.

### 6. Validate and write

Compose the output following `user-stories.schema.json`. Validate. Fix if invalid.
Write to `output_path`.

## Output artifacts

- `{output_path}` — validated user stories JSON (user-stories.schema.json)
