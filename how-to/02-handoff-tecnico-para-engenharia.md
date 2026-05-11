# 02 — Handoff técnico para engenharia

Você finalizou uma tela e precisa documentá-la para o time de engenharia. Em vez de preencher spec manualmente, você passa o link do frame e o agente gera o documento completo — componentes, tokens, estados e mapeamento para o código.

## Fluxo técnico

```
Você:  "gera o handoff dessa tela de checkout: figma.com/..."
         ↓
Claude lê o frame:
  get_design_context   →  hierarquia de componentes, propriedades, variantes usadas
  get_screenshot       →  imagem visual da tela
  get_variable_defs    →  tokens: cores, espaçamento, tipografia
  get_code_connect_map →  componente Figma → componente de código (caminho no repo)
         ↓
Claude gera o documento:
  → componentes usados com caminho no codebase
  → tokens aplicados por seção
  → estados da tela identificados (default, loading, erro, vazio)
  → notas de acessibilidade
         ↓
Publicação (opcional):
  Notion               →  página de handoff vinculada ao frame
  Slack                →  notificação ao canal do time de engenharia
```

## Como ficaria na prática

```
Você:
  Gera o handoff dessa tela pra engenharia:
  https://figma.com/design/XYZ.../checkout?node-id=123-456

Claude:
  ## Handoff — Tela de Checkout

  **Componentes**
  - Header           → src/components/Header/index.tsx
  - ProductCard      → src/components/ProductCard/index.tsx (variant="compact")
  - Button/Primary   → src/components/Button/index.tsx (variant="primary")
  - PriceSummary     → src/components/PriceSummary/index.tsx

  **Tokens aplicados**
  - Background: color.neutral.0 (#FFFFFF)
  - Padding container: spacing.6 (24px)
  - Tipografia título: text.heading.md (Inter 20/28, weight 600)

  **Estados identificados**
  - Default (frame mapeado)
  - Loading: skeletons em ProductCard (frame "Checkout/Loading")
  - Erro de pagamento: alerta inline (frame "Checkout/Error")
  - Carrinho vazio (frame "Checkout/Empty")

  **Notas de acessibilidade**
  - Botão "Confirmar pedido" sem aria-label explícito — recomendar revisão
  - Contraste do preço total (gray-500 sobre branco): 3.8:1 — abaixo do AA para texto normal
```

## O que o agente resolve bem

- Elimina o handoff manual — spec gerada direto do design real, não de memória
- Vincula componentes Figma ao código via Code Connect — engenharia sabe exatamente o que usar
- Detecta estados espalhados em frames separados e os lista automaticamente
- Sinaliza problemas de acessibilidade sem precisar de ferramenta extra

## Limitações

- Code Connect precisa estar configurado no repositório para o mapeamento código funcionar
- Estados precisam estar nomeados de forma consistente nos frames do Figma para serem detectados
- Publicação no Notion e Slack requerem os MCPs configurados no workspace
