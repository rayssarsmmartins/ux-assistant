# 03 — Auditoria de conformidade com Design System

Você quer saber se um arquivo ou tela está usando os componentes e tokens corretos do DS — ou se alguém criou elementos fora da biblioteca. O agente escaneia o design e devolve um relatório com o que está fora do padrão.

## Fluxo técnico

```
Você:  "audita esse arquivo e me fala o que está fora do DS: figma.com/..."
         ↓
Claude escaneia o arquivo:
  search_design_system →  carrega lista de componentes e tokens da biblioteca oficial
  get_design_context   →  por frame: componentes usados, valores aplicados
  get_variable_defs    →  detecta valores hard-coded (cores hex, px fixos)
         ↓
Claude compara:
  → componentes custom vs instâncias do DS
  → tokens vs valores hard-coded
  → variantes inexistentes na biblioteca
         ↓
Claude gera o relatório:
  → score de conformidade por frame
  → lista de problemas com localização (frame / layer)
  → sugestão de substituição para cada problema
         ↓
Ação opcional:
  use_figma            →  corrige automaticamente os problemas aprovados por você
  Notion               →  publica relatório de auditoria
```

## Como ficaria na prática

```
Você:
  Audita essa tela e me fala o que está fora do design system:
  https://figma.com/design/XYZ.../checkout?node-id=123-456

Claude:
  ## Auditoria — Tela de Checkout
  Conformidade: 74% (8 problemas encontrados)

  **Componentes fora do DS (3)**
  - Layer "btn-confirmar": retângulo + texto custom
    → substituir por DS/Button variant=Primary
  - Layer "tag-desconto": componente local não publicado
    → substituir por DS/Badge variant=Promo
  - Layer "input-cupom": input custom com border hard-coded
    → substituir por DS/Input variant=Default

  **Tokens não aplicados (5)**
  - "Resumo/Total" usa #1A1A1A → deveria ser color.neutral.900
  - "Frete grátis" usa #00AA55 → deveria ser color.feedback.success.default
  - Padding lateral do container: 16px fixo → deveria ser spacing.4

  Quer que eu corrija os itens de componentes agora?

Você:
  Corrige os 3 componentes fora do DS

Claude:
  → btn-confirmar substituído por DS/Button/Primary
  → tag-desconto substituída por DS/Badge/Promo
  → input-cupom substituído por DS/Input/Default
  [screenshot atualizado]
  Ficou assim. Os tokens ainda precisam de correção manual nas propriedades de texto.
```

## O que o agente resolve bem

- Detecta elementos criados fora da biblioteca sem precisar inspecionar layer por layer
- Diferencia componente fora do DS de instância desatualizada — são problemas diferentes
- Permite corrigir em lote os problemas aprovados diretamente no chat

## Limitações

- Tokens em propriedades de texto (cor de fonte, line-height) têm detecção menos precisa no beta
- Correção automática de tokens exige Full seat; relatório de auditoria funciona com Dev seat
- Componentes de bibliotecas externas (ícones de terceiros, por exemplo) podem gerar falsos positivos
