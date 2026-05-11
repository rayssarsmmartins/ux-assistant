# 04 — Consolidação de feedback de stakeholders

Você compartilhou um arquivo Figma com o time e os comentários foram chegando de produto, engenharia e negócio. Em vez de ler comentário por comentário, você pede ao agente um digest estruturado — o que está pedindo mudança, o que é dúvida, o que é aprovação.

## Fluxo técnico

```
Você:  "consolida os comentários dessa tela dos últimos 7 dias: figma.com/..."
         ↓
Claude lê os comentários:
  REST API /comments   →  todos os comentários do arquivo com autor, data e posição
  get_screenshot       →  screenshot de cada frame com comentários para contexto visual
         ↓
Claude classifica e agrupa:
  → por frame/seção da tela
  → por tipo: mudança solicitada / dúvida / aprovação / blocker
  → por autor e papel (design, produto, engenharia)
         ↓
Claude gera o digest:
  → resumo executivo (quantos itens, quantos blockers)
  → lista por frame com prioridade sugerida
  → itens que precisam de decisão antes de avançar
         ↓
Publicação (opcional):
  Slack                →  resumo no canal do projeto
  Notion               →  log estruturado vinculado ao arquivo
```

## Como ficaria na prática

```
Você:
  Consolida os comentários dessa tela dos últimos 7 dias:
  https://figma.com/design/XYZ.../checkout

Claude:
  ## Digest de Feedback — Checkout
  Período: 27 mar – 3 abr | 12 comentários de 5 pessoas

  **Resumo**
  - 4 mudanças solicitadas (1 blocker)
  - 3 dúvidas sem resposta
  - 5 aprovações

  **Blockers (1)**
  - Frame "Checkout/Pagamento" — @pedro.pm:
    "O fluxo de erro de cartão não está mapeado. Precisamos disso antes do sprint."

  **Mudanças solicitadas (3)**
  - Frame "Checkout/Resumo" — @ana.eng:
    "Botão de confirmar precisa de estado disabled enquanto carrega."
  - Frame "Checkout/Endereço" — @carlos.design:
    "Campo CEP deveria usar o componente DS/Input/Masked, não o Default."
  - Frame "Checkout/Resumo" — @lucia.ux:
    "Hierarquia do preço total vs subtotal está invertida visualmente."

  **Dúvidas sem resposta (3)**
  - "O skeleton do ProductCard está aprovado para usar nessa tela?" — @ana.eng
  - "Qual o comportamento esperado se o frete não for calculado?" — @pedro.pm
  - "Precisamos do estado de cupom inválido nesse sprint?" — @carlos.design

  Quer que eu responda alguma dúvida ou crie tarefas para as mudanças?
```

## O que o agente resolve bem

- Elimina a leitura linear de comentários — você vai direto para o que importa
- Identifica blockers automaticamente por linguagem e contexto
- Separa dúvidas sem resposta de aprovações, evitando que pedidos se percam
- Vincula cada item ao frame específico para facilitar a localização

## Limitações

- A classificação por tipo (blocker, dúvida, aprovação) é inferida pelo conteúdo — comentários ambíguos podem ser classificados incorretamente
- Comentários resolvidos (resolved) não são incluídos por padrão — peça explicitamente se quiser o histórico completo
- Criação de tarefas no Jira ou Notion requer os MCPs configurados
