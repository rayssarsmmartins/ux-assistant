---
description: Converte um diagrama de fluxo FigJam em user stories com critérios de aceite
argument-hint: <figjam-url> [epic: "Nome do épico"]
---

# flow-to-stories

Lê um diagrama FigJam, interpreta o fluxo de usuário e gera user stories no
formato "Como [ator], quero [ação], para [benefício]" com critérios Given/When/Then.
Publicação opcional no Jira e Notion.

## Prerequisites

- Figma MCP (`mcp__figma__`) acessível — o URL deve ser um board FigJam (`/board/`)
- (Opcional) Jira MCP para criação de issues
- (Opcional) Notion MCP para publicação da spec

## Agents

- `figjam-to-stories` (sonnet)

## Runtime artifacts

```
/tmp/flow-to-stories/{run_ts}/
  user-stories.json    ← stories validadas (user-stories.schema.json)
```

## Pipeline

```
[Você: URL FigJam + epic name]
        ↓
figjam-to-stories
  get_figjam + get_screenshot
  interpreta grafo → traça paths → gera stories → valida → write
        ↓
[Checkpoint: revisão das stories]
        ↓
Publicação (opcional)
  Jira: cria issues por story
  Notion: página de spec com todas as stories
```

## Lead responsibilities

1. Extrair `figjam_url` e `epic_name` dos argumentos.
   - Validar que a URL contém `/board/` — se for `/design/`, informar: "Use a URL do FigJam board, não do arquivo de design."
   - Se `epic_name` não fornecido, perguntar antes de continuar.

2. Verificar conectividade:
   ```
   mcp__figma__whoami → deve retornar identidade do usuário
   ```

3. Criar diretório de run:
   ```
   run_ts = timestamp ISO
   output_dir = /tmp/flow-to-stories/{run_ts}/
   ```

4. Spawnar `figjam-to-stories` com:
   - `figjam_url`
   - `epic_name`
   - `output_path` = `{output_dir}/user-stories.json`

5. Aguardar conclusão. Verificar que `user-stories.json` existe e é JSON válido.

6. **Checkpoint — apresentar stories:**
   Apresentar em pt-BR:
   - Resumo: número de stories, atores identificados, paths mapeados
   - Listar todas as stories no formato completo com critérios de aceite
   - Destacar `open_questions` de cada story
   - Perguntar:
     - "Quer ajustar alguma story antes de publicar?"
     - "Quer criar as issues no Jira e/ou publicar no Notion?"

7. Se ajustes solicitados:
   - Aplicar edições no JSON em memória (não re-spawnar o agente)
   - Reapresentar as stories modificadas para confirmação

8. Se publicação no Jira aprovada:
   - Para cada story em `user-stories.json`:
     - Criar issue no Jira com título = `[id] Como [actor]...`, descrição com critérios
     - Tipo: Story; Epic link: `epic_name`
   - Coletar URLs das issues criadas

9. Se publicação no Notion aprovada:
   - Criar página no `NOTION_HANDOFF_DB_ID` com todas as stories formatadas
   - Incluir link para o FigJam e links das issues Jira (se criadas)

10. Reportar conclusão em pt-BR:
    - Número de stories criadas
    - Links das issues Jira (se criadas)
    - Link da página Notion (se criada)
    - Lista de `open_questions` que precisam de resposta antes do sprint

## Failure handling

- **URL não é FigJam (`/board/`):** parar imediatamente, orientar o usuário.
- **Diagrama sem nós rotulados:** informar que o FigJam precisa de stickies ou labels para gerar stories com qualidade — entregar o que for possível com aviso.
- **Jira MCP indisponível:** continuar sem criar issues; entregar JSON local.
- **Notion indisponível:** continuar sem publicar; entregar JSON local.

## References

- Agent: `.claude/agents/figjam-to-stories.md`
- Schema: `.claude/references/flow-to-stories/schemas/user-stories.schema.json`
- Skill: `ux-communication`
