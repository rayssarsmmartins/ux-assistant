---
description: Audita conformidade de um arquivo ou frame Figma com o Design System
argument-hint: <figma-url> [scope: frame | file] [fix: yes | no]
---

# design-system-audit

Escaneia um arquivo ou frame Figma e produz um relatório de conformidade com o DS —
componentes fora da biblioteca, tokens hard-coded, nomenclatura e itens desatualizados.
Publicação opcional no Notion. Correção automática opcional via `use_figma`.

## Prerequisites

- Figma MCP (`mcp__figma__`) acessível — verificar com `mcp__figma__whoami`
- `FIGMA_PAT` no `.env` (não é necessário para MCP, mas pode ser usado em queries adicionais)
- (Opcional) Notion MCP para publicação do relatório

## Agents

- `design-system-auditor` (sonnet)

## Runtime artifacts

```
/tmp/design-system-audit/{run_ts}/
  audit-report.json    ← relatório validado (audit-report.schema.json)
```

## Pipeline

```
[Você: URL + scope + fix]
        ↓
design-system-auditor
  get_metadata → por frame: get_design_context + get_variable_defs
  search_design_system → classifica + calcula score → valida → write
        ↓
[Checkpoint: revisão do relatório]
        ↓
  (se fix=yes) use_figma → corrige por lote com aprovação
        ↓
Publicação (opcional)
  Notion: página de relatório de auditoria
```

## Lead responsibilities

1. Extrair `figma_url`, `scope` (default: `"frame"`), e `fix` (default: `false`) dos argumentos.

2. Verificar conectividade:
   ```
   mcp__figma__whoami → deve retornar identidade do usuário autenticado
   ```
   Se falhar, parar e reportar.

3. Criar diretório de run:
   ```
   run_ts = timestamp ISO
   output_dir = /tmp/design-system-audit/{run_ts}/
   ```

4. Spawnar `design-system-auditor` com:
   - `figma_url`
   - `scope`
   - `output_path` = `{output_dir}/audit-report.json`
   - `auto_correct` = `false` (correção gerenciada pelo lead no checkpoint)

5. Aguardar conclusão. Verificar que `audit-report.json` existe e é JSON válido.

6. **Checkpoint — apresentar relatório:**
   Resumir em pt-BR:
   - Score geral de conformidade (%)
   - Número de críticos, alertas e sugestões
   - Os 3 frames com menor score
   - Listar todos os itens críticos

   Perguntar:
   - "Quer que eu corrija os itens marcados como `auto_correctable` automaticamente?"
   - "Quer publicar o relatório no Notion?"

7. Se correção aprovada:
   - Re-spawnar `design-system-auditor` com `auto_correct = true`
   - Aguardar conclusão e verificar screenshot de cada frame corrigido

8. Se publicação aprovada:
   - Criar página no `NOTION_AUDIT_DB_ID` com o conteúdo do relatório
   - Incluir link para o arquivo Figma e o score geral

9. Reportar conclusão em pt-BR:
   - Score antes e depois (se correção foi aplicada)
   - Link para a página Notion (se publicado)
   - Itens que requerem correção manual (não `auto_correctable`)

## Failure handling

- **MCP indisponível:** parar e reportar.
- **Arquivo muito grande (>50 frames):** auditar apenas os 10 primeiros frames e informar o usuário — oferecer auditar por página ou por frame específico.
- **Notion indisponível:** entregar `audit-report.json` localmente, informar.
- **Correção via `use_figma` falhar:** registrar o item como "correção manual necessária", continuar com os demais.

## References

- Agent: `.claude/agents/design-system-auditor.md`
- Schema: `.claude/references/design-system-audit/schemas/audit-report.schema.json`
- Rules: `.claude/references/design-system-audit/audit-rules.md`
- Skills: `figma-context`, `design-system-governance`, `ux-communication`
