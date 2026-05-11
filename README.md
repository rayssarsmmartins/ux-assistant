# UX Assistant

Workspace de agente Senior UX Designer assistido por IA, com o Figma como sistema central.
Conecta design, engenharia e produto através de workflows automatizados que leem e escrevem
no Figma, geram documentação, auditam conformidade com o Design System e convertem diagramas
em artefatos de produto.

## Objetivos

- **Eliminar trabalho manual repetitivo** — handoff, auditoria de DS, consolidação de feedback
- **Manter consistência** — garantir que telas usem os componentes e tokens certos antes do handoff
- **Fechar o gap design ↔ código** — Code Connect sincronizado, specs com caminhos reais do codebase
- **Documentar o processo** — FigJam vira user stories, comentários viram digest estruturado
- **Ser iterativo no chat** — ajustes em telas existentes direto na conversa, sem abrir o Figma manualmente

## Workflows disponíveis

| Comando | O que faz | Seat Figma |
|---|---|---|
| `/design-handoff` | Gera spec técnica completa de uma tela para engenharia | Dev |
| `/design-system-audit` | Audita conformidade de arquivo ou frame com o DS | Dev / Full (correção) |
| `/feedback-digest` | Consolida e classifica comentários de um arquivo Figma | Qualquer |
| `/flow-to-stories` | Converte diagrama FigJam em user stories com critérios de aceite | Dev |
| `/code-connect-sync` | Sincroniza mapeamentos DS Figma ↔ componentes de código | Dev / Full |
| `/diagram-from-description` | Cria diagrama FigJam a partir de descrição em texto | Full |

Além dos comandos, você pode pedir melhorias diretamente em telas existentes passando
o link do frame no chat — veja os exemplos em `how-to/`.

## Pré-requisitos

- [Claude Code](https://claude.ai/code) instalado
- Conta Figma com **Dev seat** (mínimo para leitura) ou **Full seat** (para escrita no canvas)
- Figma desktop app aberto quando usar comandos que escrevem no canvas (`/diagram-from-description`, correção em `/design-system-audit`)
- Plano Figma **Professional ou superior** (rate limits viáveis para uso de agente)

## Primeiros passos

### 1. Configurar credenciais

Copie o arquivo de exemplo e preencha com seus dados:

```bash
cp .env.example .env
```

Abra `.env` e preencha:

```bash
# Obrigatório para todos os workflows de leitura
FIGMA_PAT=figd_xxxxxxxxxxxx   # Settings > Security > Personal access tokens

# Obrigatório para auditoria e Code Connect
FIGMA_TEAM_ID=123456789       # URL do Figma: figma.com/files/team/{ID}
FIGMA_DS_FILE_KEY=AbCdEfGhIj  # URL do arquivo DS: figma.com/design/{FILE_KEY}/...

# Opcional — para publicação de specs e relatórios
NOTION_HANDOFF_DB_ID=...
SLACK_ENG_CHANNEL=#frontend
```

Para gerar um Personal Access Token:
1. No Figma, acesse **Settings → Security → Personal access tokens**
2. Clique em **Generate new token**
3. Selecione os escopos: `file_content:read`, `file_comments:read`, `file_comments:write`
4. Copie o token gerado — ele não será exibido novamente

### 2. Conectar o Figma MCP

O arquivo `.claude/settings.local.json` já vem configurado com o MCP do Figma.
Na primeira vez que rodar um workflow, o Claude Code pedirá autenticação via browser.

Para verificar se a conexão está funcionando, rode no chat:

```
mcp__figma__whoami
```

Deve retornar seu nome de usuário e tipo de seat.

### 3. Preencher o contexto do seu time

Edite os arquivos de knowledge com os dados reais do seu time e DS:

- `.claude/knowledge/team/people.md` — designers, PMs e engenheiros com seus handles do Figma e Slack
- `.claude/knowledge/design-system/components.md` — inventário de componentes da biblioteca
- `.claude/knowledge/design-system/tokens.md` — tokens de cor, espaçamento e tipografia

Esses arquivos são carregados pelos agentes para garantir nomenclatura correta e contexto
de equipe nas specs e relatórios gerados.

---

## Exemplo de uso: testando o `/design-handoff`

Veja como testar com um frame real do Figma.

### Passo 1 — Copie o link de um frame

No Figma, selecione um frame (não um componente isolado — um frame de tela completa funciona melhor).
Clique com o botão direito → **Copy link to selection**.

O link terá este formato:
```
https://www.figma.com/design/AbCdEfGhIj/Nome-do-Projeto?node-id=42-100&...
```

### Passo 2 — Rode o comando no Claude Code

Abra o Claude Code neste diretório e escreva:

```
/design-handoff https://www.figma.com/design/AbCdEfGhIj/Nome-do-Projeto?node-id=42-100
```

### Passo 3 — Acompanhe o agente

O agente vai:

1. Ler a estrutura do frame (`get_design_context`)
2. Capturar um screenshot (`get_screenshot`)
3. Extrair os tokens aplicados (`get_variable_defs`)
4. Buscar os mapeamentos de código (`get_code_connect_map`)
5. Identificar estados (frames irmãos com nomes como `Loading`, `Error`, `Empty`)
6. Verificar pontos de acessibilidade (contraste, áreas de toque, labels)

### Passo 4 — Revisar o checkpoint

O agente apresenta um resumo antes de qualquer publicação:

```
## Handoff — Tela de Checkout

Componentes mapeados: 5
Tokens identificados: 12
Estados encontrados: Default, Loading, Error
Problemas de acessibilidade críticos: 1

  CRÍTICO: botão "Confirmar" com contraste 2.8:1 (mínimo AA: 4.5:1)

Quer publicar no Notion e notificar a engenharia?
```

Responda `sim` para publicar ou `não` para só receber o arquivo local em
`/tmp/design-handoff/{timestamp}/spec.json`.

### Passo 5 — Resultado esperado

A spec gerada terá esta estrutura:

```json
{
  "screen_name": "Checkout — Default",
  "figma_url": "https://figma.com/design/...",
  "status": "ready_for_dev",
  "components": [
    {
      "figma_name": "DS/Button",
      "variant": "Primary",
      "code_path": "src/components/Button/index.tsx",
      "notes": null
    }
  ],
  "tokens": {
    "color": [
      { "element": "Background", "token": "color.neutral.0", "value": "#FFFFFF" }
    ],
    "hardcoded_violations": [
      {
        "element": "Preço total",
        "current_value": "#1A1A1A",
        "recommended_token": "color.neutral.900"
      }
    ]
  },
  "states": [
    { "name": "Default", "figma_url": "https://figma.com/..." },
    { "name": "Loading", "figma_url": "https://figma.com/..." }
  ],
  "accessibility": {
    "critical": [
      {
        "element": "Botão Confirmar",
        "issue": "Contraste 2.8:1 com fundo branco",
        "fix": "Usar color.neutral.700 (#595959) para atingir 4.5:1"
      }
    ],
    "warning": [],
    "suggestion": []
  }
}
```

---

## Estrutura do projeto

```
ux-assistant/
├── README.md
├── .env.example                        ← template de credenciais
├── how-to/                             ← guias de uso por workflow (pt-BR)
│   ├── 01-melhoria-em-tela-existente.md
│   ├── 02-handoff-tecnico-para-engenharia.md
│   ├── 03-auditoria-de-conformidade-com-design-system.md
│   ├── 04-consolidacao-de-feedback-de-stakeholders.md
│   ├── 05-figjam-para-user-stories.md
│   ├── 06-review-de-acessibilidade.md
│   └── 07-criacao-de-diagrama-a-partir-de-descricao.md
└── .claude/
    ├── settings.local.json             ← MCP do Figma + permissões
    ├── rules/                          ← regras globais do workspace
    ├── knowledge/                      ← contexto do time e do DS
    │   ├── team/people.md
    │   └── design-system/
    │       ├── components.md
    │       └── tokens.md
    ├── skills/                         ← capacidades reutilizáveis
    │   ├── figma-context/SKILL.md
    │   ├── ux-communication/SKILL.md
    │   ├── design-system-governance/SKILL.md
    │   └── accessibility-review/SKILL.md
    ├── agents/                         ← subagentes especializados
    │   ├── figma-spec-extractor.md
    │   ├── design-system-auditor.md
    │   ├── feedback-consolidator.md
    │   ├── figjam-to-stories.md
    │   └── code-connect-mapper.md
    ├── commands/                       ← entrypoints dos workflows
    │   ├── design-handoff.md
    │   ├── design-system-audit.md
    │   ├── feedback-digest.md
    │   ├── flow-to-stories.md
    │   ├── code-connect-sync.md
    │   └── diagram-from-description.md
    └── references/                     ← schemas e guias de suporte
        ├── design-handoff/
        ├── design-system-audit/
        ├── feedback-digest/
        ├── flow-to-stories/
        └── code-connect/
```

## Guias de uso

Cada arquivo em `how-to/` documenta um workflow com fluxo técnico, exemplo de conversa
real e limitações. Leia antes de usar um comando pela primeira vez.

## Limitações conhecidas

- **Escrita no canvas** (`use_figma`, `generate_diagram`) requer Full seat e o Figma desktop aberto
- **Fontes customizadas** podem usar fallback se não instaladas localmente
- **Code Connect** requer configuração prévia no repositório de código para o mapeamento funcionar
- **Variáveis de token** em textos têm detecção menos precisa que em preenchimentos de forma
- **Rate limits**: plano Starter ou seat View tem limite de 6 chamadas MCP por mês — inviável para uso contínuo
