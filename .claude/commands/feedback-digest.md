---
description: Consolida e classifica os comentários de um arquivo Figma em um digest estruturado
argument-hint: <figma-file-url> [days: 7 | 14 | 30]
---

# feedback-digest

Lê todos os comentários de um arquivo Figma dentro de um período, classifica por tipo
(blocker, mudança, dúvida, aprovação) e autor, e produz um digest estruturado.
Publicação opcional no Slack e Notion.

## Prerequisites

- `FIGMA_PAT` no `.env` — obrigatório (comentários são lidos via REST API)
- Figma MCP (`mcp__figma__`) para metadata dos frames
- (Opcional) Slack MCP para notificação
- (Opcional) Notion MCP para log estruturado

## Agents

- `feedback-consolidator` (sonnet)

## Runtime artifacts

```
/tmp/feedback-digest/{run_ts}/
  digest.json    ← digest validado (feedback-digest.schema.json)
```

## Pipeline

```
[Você: URL + período]
        ↓
feedback-consolidator
  REST API /comments → get_metadata (frame names)
  classifica + agrupa por frame → valida → write
        ↓
[Apresentar digest ao usuário]
        ↓
Publicação (opcional)
  Slack: resumo executivo no canal
  Notion: log completo
```

## Lead responsibilities

1. Extrair `figma_url` e `days` dos argumentos (default: `7`).
   - Calcular `period_from` = hoje − `days` dias (ISO date).
   - Calcular `period_to` = hoje (ISO date).
   - Extrair `figma_file_key` da URL.

2. Verificar que `FIGMA_PAT` está presente no `.env`.
   Se ausente, parar: "Configure `FIGMA_PAT` no `.env` — necessário para leitura de comentários."

3. Criar diretório de run:
   ```
   run_ts = timestamp ISO
   output_dir = /tmp/feedback-digest/{run_ts}/
   ```

4. Spawnar `feedback-consolidator` com:
   - `figma_url`
   - `figma_file_key`
   - `period_from`, `period_to`
   - `output_path` = `{output_dir}/digest.json`
   - `figma_pat` = valor de `FIGMA_PAT`

5. Aguardar conclusão. Verificar que `digest.json` existe e é JSON válido.

6. Apresentar digest em pt-BR:
   - Resumo executivo (total, blockers, mudanças, dúvidas sem resposta)
   - Blockers em destaque (se houver)
   - Mudanças solicitadas por frame
   - Dúvidas sem resposta (needs_decision = true)
   - Perguntar: "Quer publicar no Slack e/ou Notion?"

7. Se publicação no Slack aprovada:
   - Enviar resumo executivo no `SLACK_DESIGN_CHANNEL`
   - Incluir link para o arquivo Figma e o período coberto

8. Se publicação no Notion aprovada:
   - Criar página no `NOTION_HANDOFF_DB_ID` com o digest completo estruturado

9. Reportar conclusão em pt-BR com os principais itens de ação.

## Failure handling

- **FIGMA_PAT ausente ou inválido:** parar imediatamente, instruir o usuário a configurar.
- **Nenhum comentário no período:** informar "Nenhum comentário encontrado entre {from} e {to}." Não gerar artefato vazio.
- **Slack ou Notion indisponíveis:** entregar digest localmente, informar.

## References

- Agent: `.claude/agents/feedback-consolidator.md`
- Schema: `.claude/references/feedback-digest/schemas/feedback-digest.schema.json`
- Skill: `ux-communication`
- Knowledge: `.claude/knowledge/team/people.md`
