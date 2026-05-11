# 01 — Melhoria em tela existente

Você tem uma tela no Figma que precisa de ajuste — falta um componente, um estado, ou um elemento está errado. Você passa o link do frame e descreve o que precisa mudar.

## Fluxo técnico

```
Você:  "essa tela não tem botão primário de 'Confirmar pedido': figma.com/..."
         ↓
Claude lê o frame:
  get_design_context   →  estrutura de layers, componentes, tokens
  get_screenshot       →  imagem visual da tela atual
  search_design_system →  encontra o componente correto na biblioteca do DS
         ↓
Claude escreve no canvas:
  use_figma            →  insere a instância do componente, texto e posição certos
         ↓
Claude valida:
  get_screenshot       →  screenshot após a mudança para você confirmar
```

## Como ficaria na prática

```
Você:
  Adiciona um botão primário "Confirmar pedido" no rodapé dessa tela:
  https://figma.com/design/XYZ.../checkout?node-id=123-456

Claude:
  → frame tem: Header, ProductList, PriceSummary — sem CTA no rodapé
  → encontrei DS/Button variant=Primary na biblioteca
  → adicionei com margin-bottom=24, alinhado ao grid de 16px
  [screenshot com o botão inserido]
  Quer ajustar posição, texto ou variante?

Você:
  Centraliza horizontalmente e muda o texto para "Finalizar compra"

Claude:
  → atualizado: alinhamento center, label="Finalizar compra"
  [screenshot atualizado]
```

## O que o agente resolve bem

- Busca o componente certo no DS antes de inserir — não inventa nada fora da biblioteca
- Lê o contexto visual da tela antes de agir, entende onde há espaço
- Permite iterar no mesmo chat sem reabrir o Figma manualmente

## Limitações

- Escrita no canvas exige **Full seat** no Figma e o arquivo aberto no desktop app
- Fontes customizadas não instaladas no sistema podem usar fallback
- Mudanças de layout complexas (reorganizar hierarquia, grid responsivo) têm fidelidade limitada no beta
