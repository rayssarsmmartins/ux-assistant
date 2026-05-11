---
name: ux-communication
description: >
  How to write design specs, handoff notes, audit reports, and Figma comments
  that are clear and actionable for engineering, product, and design audiences.
  Triggers: any task that produces written design documentation or feedback.
---

# UX Communication Skill

## When to activate

Activate when the task involves:
- Writing a handoff spec for engineering
- Generating an audit report
- Composing Figma comments or feedback
- Summarizing design decisions for stakeholders

## Audience-specific writing

### For engineering

- Lead with what they need to implement: component name, variant, token, behavior.
- Be explicit about states: list every state a component can be in (default, hover, focus, disabled, loading, error, empty).
- Reference code: use Code Connect paths when available (`src/components/Button/index.tsx`).
- Avoid design jargon: say "padding of 24px (spacing.6)" not "generous breathing room".

### For product

- Lead with user impact: what the change enables or fixes.
- Reference the user story or job-to-be-done.
- Flag open decisions and blockers clearly — use bold or a dedicated section.

### For design team

- Use DS terminology: component names, token names, variant names exactly as they appear in Figma.
- Flag non-compliant elements with specific correction ("replace with DS/Button/Primary").
- Distinguish between critical issues (blocks handoff) and suggestions (improves quality).

## Handoff spec structure

```markdown
## Handoff — [Screen Name]

**Frame:** [Figma URL with node-id]
**Status:** Ready for dev / In review / Blocked

### Components
- [ComponentName] → [code path] (variant=[VariantName])

### Tokens applied
- [Element]: [token-name] ([value])

### States
- Default: [frame URL or description]
- Loading: [frame URL or description]
- Error: [frame URL or description]
- Empty: [frame URL or description]

### Accessibility notes
- [Issue]: [recommendation]

### Open questions
- [Question] — owner: @[person]
```

## Audit report structure

```markdown
## Audit — [File/Screen Name]
Conformidade: [X]% | [N] problemas encontrados

### Críticos
- [Layer name]: [problem] → [correction]

### Alertas
- [Layer name]: [problem] → [recommendation]

### Sugestões
- [Layer name]: [observation]
```

## Writing Figma comments

- Be specific about the layer: "Layer 'btn-confirmar' in frame 'Checkout/Default'"
- State the problem before the solution: "This uses a hard-coded hex (#FF0000) — should use color.feedback.error.default"
- One issue per comment — don't batch multiple problems in one comment.
- For blockers, start with "BLOCKER:" so it's visible in digest summaries.
- Use @mention for the person who needs to act.

## Severity classification

| Severity | Definition | Label |
|----------|-----------|-------|
| Critical | Blocks handoff or shipping — accessibility failure, missing state, broken DS compliance | **Crítico** |
| Warning | Degrades quality but doesn't block — wrong variant, inconsistent spacing | **Alerta** |
| Suggestion | Improvement opportunity — naming, organization, documentation | **Sugestão** |

## Accessibility notes format

When flagging accessibility issues, always include:
1. The specific element and current value
2. The standard being violated (WCAG 2.1 AA)
3. The minimum required value
4. The recommended fix

Example: "Label 'Email' uses color.neutral.400 (#9CA3AF) on white → contrast ratio 2.6:1. WCAG AA minimum: 4.5:1. Fix: use color.neutral.700 (#595959)."
