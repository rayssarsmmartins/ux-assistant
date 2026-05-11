---
name: feedback-consolidator
model: sonnet
description: >
  Reads Figma comments for a file or frame within a date range, classifies each
  comment by type and author role, and produces a structured feedback digest.
tools:
  - Read
  - Write
  - Bash
  - mcp__figma__get_screenshot
  - mcp__figma__get_metadata
skills:
  - ux-communication
---

# feedback-consolidator

Fetches Figma comments via the REST API, classifies them, and produces a validated
feedback digest JSON grouped by frame and comment type.

## Read before processing

- `.claude/skills/ux-communication/SKILL.md`
- `.claude/knowledge/team/people.md`
- `.claude/references/feedback-digest/schemas/feedback-digest.schema.json`

## Input

Received from lead:

- `figma_url` — Figma file URL (file-level, not a specific frame)
- `figma_file_key` — extracted file key from `figma_url`
- `period_from` — ISO date string (e.g. `2026-03-27`)
- `period_to` — ISO date string (e.g. `2026-04-03`)
- `output_path` — absolute path to write the digest JSON
- `figma_pat` — Figma Personal Access Token (read from `.env`)

## Procedure

### 1. Fetch comments

Call the Figma REST API:

```bash
curl -s "https://api.figma.com/v1/files/{figma_file_key}/comments" \
  -H "X-Figma-Token: {figma_pat}"
```

Parse the response. Filter comments where `created_at` is within `[period_from, period_to]`.

### 2. Fetch frame metadata for grouping

Call `get_metadata` on the file root to get the frame name for each node ID referenced
in the comments. Build a map: `{ nodeId → frame_name }`.

### 3. Classify each comment

For each comment, apply the classification rules from `ux-communication` SKILL.md:

**Type classification** (infer from text content):
- `blocker` — contains "BLOCKER", "bloquei", "bloqueado", "não podemos avançar", "precisa resolver antes"
- `change_requested` — imperative requests ("adiciona", "remove", "muda", "ajusta", "corrige")
- `question` — contains "?", "como", "qual", "quando", "por que", "precisa de"
- `approval` — contains "aprovado", "ok", "looks good", "LGTM", ":white_check_mark:"
- `info` — informational statements that don't fit the above

**Author role** (cross-reference `people.md`):
- Match author name/handle against `people.md` entries to assign role.
- If not found: use `"other"`.

**needs_decision**:
- `true` if type is `"question"` and `reply_count = 0`

### 4. Group by frame

Build the frames array: for each unique frame name, collect all its comments sorted
by date descending (most recent first), blockers first within each frame.

### 5. Compose digest JSON

Follow `feedback-digest.schema.json`. Validate before writing.

### 6. Write output

Write validated JSON to `output_path`.

## Output artifacts

- `{output_path}` — validated feedback digest JSON (feedback-digest.schema.json)
