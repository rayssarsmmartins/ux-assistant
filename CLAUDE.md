# CLAUDE.md

UX Assistant workspace — agente Senior UX Designer com Figma como sistema central.

## Configuration

Read `.env` at the start of any task that needs credentials, file keys, or team IDs.
See `.env.example` for the variable reference.

## Language

Produce all user-facing content in pt-BR unless the task explicitly requires another language.
Write skill and agent instructions in English.

## Python

- Always run scripts with `uv run <script>`. Never use bare `python` / `python3`.
- Every `.py` file must have a PEP 723 inline metadata header (`# /// script`), even if `dependencies = []`.
- Do not use `pip install`, `venv`, or `requirements.txt`.

## Figma MCP

The Figma MCP server is configured at `https://mcp.figma.com/mcp` (Streamable HTTP).
Tool prefix: `mcp__figma__`.

### Tool quick reference

| Need | Tool |
|------|------|
| Understand structure and components of a frame | `mcp__figma__get_design_context` |
| Visual snapshot of a frame or selection | `mcp__figma__get_screenshot` |
| Lightweight layer tree (large files) | `mcp__figma__get_metadata` |
| Design tokens applied to a selection | `mcp__figma__get_variable_defs` |
| Search components, styles, variables in DS libraries | `mcp__figma__search_design_system` |
| Read a FigJam diagram | `mcp__figma__get_figjam` |
| Figma node → code component mapping | `mcp__figma__get_code_connect_map` |
| Write or modify objects on the canvas (Full seat) | `mcp__figma__use_figma` |
| Create a FigJam diagram from text (Full seat) | `mcp__figma__generate_diagram` |

### Seat requirements

- **Dev seat**: all `get_*` and `search_*` tools (read-only)
- **Full seat**: `use_figma`, `generate_diagram`, `generate_figma_design`, `create_new_file`

## On-demand resources

- `.claude/knowledge/` — team, design system, and channel context (load when needed)
- `.claude/skills/` — reusable capabilities (load the relevant SKILL.md when its trigger matches)

## How-to flows

User-facing workflow guides live in `how-to/`. Each file documents a specific use case with
the technical flow, a practical example, and limitations.
