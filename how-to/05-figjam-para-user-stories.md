# 05 — FigJam para User Stories

Você tem um diagrama de fluxo de usuário no FigJam — telas, decisões, caminhos alternativos — e precisa transformar isso em user stories com critérios de aceite para o time de produto e engenharia.

## Fluxo técnico

```
Você:  "transforma esse fluxo de checkout em user stories: figma.com/..."
         ↓
Claude lê o diagrama:
  get_figjam           →  estrutura XML do diagrama com nós, conexões e labels
  get_screenshot       →  imagem visual do fluxo completo
         ↓
Claude interpreta o fluxo:
  → identifica atores (quem executa cada ação)
  → mapeia caminhos felizes e alternativos
  → detecta pontos de decisão e estados de erro
         ↓
Claude gera as user stories:
  → formato: "Como [ator], quero [ação], para [benefício]"
  → critérios de aceite no formato EARS (Event-Action-Response-State)
  → stories agrupadas por épico/fluxo
         ↓
Publicação (opcional):
  Jira                 →  cria issues com stories e critérios
  Notion               →  página de spec vinculada ao FigJam
```

## Como ficaria na prática

```
Você:
  Transforma esse fluxo de checkout em user stories:
  https://figma.com/board/XYZ.../fluxo-checkout

Claude:
  ## User Stories — Fluxo de Checkout
  Épico: Finalização de compra | 6 stories geradas

  **Story 1 — Visualizar resumo do pedido**
  Como comprador, quero ver o resumo do meu pedido antes de pagar,
  para confirmar os itens, quantidades e valores.

  Critérios de aceite:
  - Dado que o carrinho tem itens, quando acesso o checkout,
    então vejo lista de produtos, subtotal, frete e total
  - Dado que o frete não foi calculado, quando acesso o checkout,
    então vejo "calcular frete" com campo de CEP

  **Story 2 — Inserir endereço de entrega**
  Como comprador, quero informar meu endereço de entrega,
  para que o pedido chegue no local correto.

  Critérios de aceite:
  - Dado que preencho o CEP, quando confirmo, então os campos de
    endereço são preenchidos automaticamente via API de CEP
  - Dado que o CEP é inválido, quando confirmo,
    então vejo mensagem de erro inline no campo

  [continua para as 4 stories restantes...]

  Quer que eu crie as issues no Jira ou ajuste algum critério?
```

## O que o agente resolve bem

- Transforma trabalho visual do FigJam em artefato textual sem transcrição manual
- Detecta caminhos alternativos e erros que muitas vezes ficam de fora das stories escritas à mão
- Mantém rastreabilidade: cada story referencia o nó do FigJam de origem

## Limitações

- Diagramas muito densos ou sem labels claros nos nós geram stories genéricas — nomeie bem seus stickies e conectores no FigJam
- O agente não tem contexto de negócio sobre o que é prioridade — a ordenação das stories precisa de revisão humana
- Criação de issues no Jira requer o MCP da Atlassian configurado
