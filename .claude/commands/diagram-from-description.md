---
description: Cria um diagrama FigJam a partir de uma descrição em linguagem natural ou Mermaid
argument-hint: "<descrição do fluxo>" [type: flowchart | sequence | states | journey]
---

# diagram-from-description

Converte uma descrição textual de fluxo, sequência ou jornada em um diagrama FigJam.
Requer Full seat no Figma e o arquivo aberto no desktop app.

## Prerequisites

- Figma MCP (`mcp__figma__`) acessível com Full seat
- Arquivo Figma aberto no desktop app (o `generate_diagram` executa via Plugin API)
- `FIGMA_PROJECT_FILE_KEY` no `.env` — onde o diagrama será criado (opcional: cria em rascunhos se ausente)

## Tools used directly by lead

- `mcp__figma__generate_diagram`
- `mcp__figma__get_figjam`
- `mcp__figma__get_screenshot`

## Diagram types

| Tipo | Quando usar |
|---|---|
| `flowchart` | Fluxo de usuário com decisões e ramificações (default) |
| `sequence` | Interações entre sistemas ou atores em ordem temporal |
| `states` | Estados de uma tela ou componente com transições |
| `journey` | Jornada do usuário com etapas e sentimentos |

## Lead responsibilities

1. Extrair a descrição do usuário e o `type` (default: `flowchart`).

2. Verificar Full seat:
   ```
   mcp__figma__whoami → verificar tipo de seat no retorno
   ```
   Se seat for Dev ou View, parar: "Criar diagramas requer Full seat no Figma."

3. Converter a descrição para Mermaid (melhora a fidelidade do diagrama):

   Para `flowchart`:
   ```mermaid
   flowchart TD
     A[Início] --> B{Decisão}
     B -->|Sim| C[Passo 1]
     B -->|Não| D[Passo alternativo]
   ```

   Para `sequence`:
   ```mermaid
   sequenceDiagram
     Usuário->>Sistema: Ação
     Sistema-->>Usuário: Resposta
   ```

   Usar a descrição do usuário para preencher os nós e transições.

4. Chamar `mcp__figma__generate_diagram`:
   - Passar o Mermaid gerado como input
   - Especificar o tipo de diagrama
   - Se `FIGMA_PROJECT_FILE_KEY` disponível, criar no arquivo do projeto; caso contrário, criar em rascunhos

5. Validar o resultado:
   - Chamar `get_figjam` na URL do diagrama criado para verificar a estrutura
   - Chamar `get_screenshot` para captura visual

6. Apresentar ao usuário:
   - Screenshot do diagrama criado
   - Link direto para o board FigJam
   - Perguntar: "Quer ajustar algum nó, transição ou label?"

7. Iterar conforme necessário:
   - Para ajustes simples (renomear nó, adicionar seta), usar `mcp__figma__use_figma`
   - Para mudanças estruturais grandes, regenerar com `generate_diagram`

## Failure handling

- **Full seat ausente:** parar, informar o requisito.
- **Descrição muito vaga:** pedir ao usuário para detalhar os passos antes de gerar.
- **Diagrama gerado com nós desconexos:** informar o usuário e sugerir adicionar labels nas transições na descrição.
- **Arquivo não aberto no desktop:** informar: "Abra o arquivo Figma no desktop app antes de continuar."
