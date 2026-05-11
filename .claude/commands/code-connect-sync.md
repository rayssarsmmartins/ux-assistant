---
description: Sincroniza mapeamentos Code Connect entre a biblioteca Figma DS e o repositório de código
argument-hint: <repo-path> [components-root: src/components]
---

# code-connect-sync

Descobre componentes do DS que ainda não têm Code Connect configurado, encontra os
componentes de código correspondentes no repositório, valida os candidatos e envia
os mapeamentos confirmados para o Figma.

## Prerequisites

- Figma MCP (`mcp__figma__`) acessível
- `FIGMA_DS_FILE_KEY` no `.env` — chave do arquivo da biblioteca DS
- Acesso de leitura ao repositório de código em `repo-path`
- Full seat ou Dev seat (leitura de sugestões funciona com Dev; envio de mapeamentos requer permissão de edição no arquivo DS)

## Agents

- `code-connect-mapper` (sonnet)

## Runtime artifacts

```
/tmp/code-connect-sync/{run_ts}/
  mapping-report.json    ← relatório completo de mapeamentos
```

## Pipeline

```
[Você: repo-path + components-root]
        ↓
code-connect-mapper
  get_code_connect_map → search_design_system
  Glob + Grep no repositório → get_code_connect_suggestions
  classifica: confirmed / needs_review / not_found
        ↓
[Checkpoint: revisão dos mapeamentos]
        ↓
  send_code_connect_mappings (apenas confirmed aprovados)
        ↓
  write mapping-report.json
```

## Lead responsibilities

1. Extrair `repo_path` e `components_root` (default: `src/components`) dos argumentos.
   - Verificar que `FIGMA_DS_FILE_KEY` está presente no `.env`.
   - Se ausente, parar: "Configure `FIGMA_DS_FILE_KEY` no `.env`."

2. Verificar conectividade:
   ```
   mcp__figma__whoami → deve retornar identidade do usuário
   ```

3. Criar diretório de run:
   ```
   run_ts = timestamp ISO
   output_dir = /tmp/code-connect-sync/{run_ts}/
   ```

4. Spawnar `code-connect-mapper` com:
   - `figma_ds_file_key` = valor de `FIGMA_DS_FILE_KEY`
   - `repo_path`
   - `components_root`
   - `output_path` = `{output_dir}/mapping-report.json`

5. Aguardar. O agente **não envia mapeamentos ainda** — apenas reporta as três listas.

6. **Checkpoint — revisão dos mapeamentos:**
   Apresentar em pt-BR:

   **Mapeamentos prontos para envio (`confirmed`):**
   | Componente Figma | Arquivo de código |
   |---|---|
   | DS/Button | src/components/Button/index.tsx |
   | ... | ... |

   **Precisam de revisão manual (`needs_review`):**
   | Componente Figma | Candidato encontrado | Motivo da dúvida |
   |---|---|---|
   | DS/Input | src/components/TextInput/index.tsx | Nome diferente |
   | ... | ... | ... |

   **Não encontrados (`not_found`):**
   - Lista de componentes Figma sem candidato no código

   Perguntar:
   - "Confirma o envio dos [N] mapeamentos prontos?"
   - Para `needs_review`: "Quer confirmar manualmente algum desses?"

7. Se confirmado, re-spawnar `code-connect-mapper` em modo de envio:
   - Passar a lista `confirmed` aprovada + quaisquer itens de `needs_review` aprovados manualmente
   - Aguardar confirmação de envio

8. Reportar conclusão em pt-BR:
   - Mapeamentos enviados com sucesso
   - Erros de envio (se houver)
   - Componentes `not_found` que precisam de criação no código

## Failure handling

- **`FIGMA_DS_FILE_KEY` ausente:** parar imediatamente.
- **Repositório inacessível:** parar e reportar o caminho que falhou.
- **`send_code_connect_mappings` falha parcial:** registrar quais falharam, continuar com os demais, reportar ao final.
- **Nenhum componente novo para mapear:** informar "Todos os componentes do DS já têm Code Connect configurado."

## References

- Agent: `.claude/agents/code-connect-mapper.md`
- Guide: `.claude/references/code-connect/mapping-guide.md`
- Skill: `figma-context`
