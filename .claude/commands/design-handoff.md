---
description: Gera spec técnica completa de uma tela Figma para engenharia
argument-hint: <figma-frame-url> [stack: React+Tailwind | Vue | iOS | Android]
---

# design-handoff

Lê um frame Figma e produz uma spec de handoff completa — componentes, tokens,
estados, Code Connect e notas de acessibilidade. Publicação opcional no Notion e Slack.

## Prerequisites

- Figma MCP (`mcp__figma__`) acessível — verifique com `mcp__figma__whoami`
- `FIGMA_PAT` presente no `.env` para chamadas REST (comentários, versões)
- (Opcional) Notion MCP configurado para publicação

## Agents

- `figma-spec-extractor` (sonnet)

## Runtime artifacts

```
/tmp/design-handoff/{run_ts}/
  spec.json          ← handoff spec validada (handoff-spec.schema.json)
  screenshot.png     ← snapshot visual do frame
```

## Pipeline

```
[Você: URL do frame]
        ↓
figma-spec-extractor
  get_metadata → get_design_context → get_screenshot
  get_variable_defs → get_code_connect_map
  accessibility checks → compose spec → validate → write
        ↓
[Checkpoint: revisão da spec]
        ↓
Publicação (opcional)
  Notion: página de handoff
  Slack: notificação ao time de eng
```

## Lead responsibilities

1. Receber `figma_url` do usuário. Extrair `stack` se fornecido (default: `React + Tailwind`).

2. Verificar conectividade:
   ```
   mcp__figma__whoami → deve retornar identidade do usuário autenticado
   ```
   Se falhar, parar e reportar: "Figma MCP inacessível — verifique a configuração em `.claude/settings.local.json`."

3. Criar diretório de run:
   ```
   run_ts = timestamp ISO (ex: 2026-04-03T14-30-00)
   output_dir = /tmp/design-handoff/{run_ts}/
   ```

4. Spawnar `figma-spec-extractor` com:
   - `figma_url` = URL recebida
   - `output_path` = `{output_dir}/spec.json`
   - `target_stack` = stack extraído ou default

5. Aguardar conclusão. Verificar que `spec.json` existe e é JSON válido.

6. **Checkpoint — revisão da spec:**
   Apresentar ao usuário um resumo em pt-BR:
   - Número de componentes mapeados
   - Número de tokens identificados
   - Estados encontrados
   - Problemas de acessibilidade críticos (se houver)
   - Perguntar: "Quer publicar no Notion e notificar a engenharia?"

7. Se aprovado, publicar:
   - Notion: criar página no `NOTION_HANDOFF_DB_ID` com o conteúdo da spec
   - Slack: enviar resumo no `SLACK_ENG_CHANNEL` com link para a página Notion e o frame Figma

8. Reportar conclusão em pt-BR com:
   - Link para o frame Figma
   - Link para a página Notion (se publicado)
   - Lista de itens críticos de acessibilidade para ação imediata

## Failure handling

- **figma-spec-extractor falha na primeira tentativa:** retry uma vez com o mesmo input.
- **Schema validation falha:** reportar os campos inválidos ao usuário antes de prosseguir.
- **Notion MCP indisponível:** continuar sem publicar; entregar `spec.json` local e informar o usuário.
- **Frame sem Code Connect:** continuar normalmente; anotar "Code Connect não configurado" na spec.

## References

- Agent: `.claude/agents/figma-spec-extractor.md`
- Schema: `.claude/references/design-handoff/schemas/handoff-spec.schema.json`
- Template: `.claude/references/design-handoff/spec-template.md`
- Skills: `figma-context`, `ux-communication`
