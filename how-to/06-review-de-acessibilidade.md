# 06 — Review de acessibilidade

Você quer verificar se uma tela atende os critérios de acessibilidade antes de ir para engenharia. O agente analisa contraste, tamanhos de toque, hierarquia visual e sinaliza o que está abaixo do padrão WCAG 2.1 AA.

## Fluxo técnico

```
Você:  "faz um review de acessibilidade nessa tela: figma.com/..."
         ↓
Claude lê o frame:
  get_design_context   →  cores aplicadas, tamanhos de texto, componentes interativos
  get_screenshot       →  imagem da tela para análise visual
  get_variable_defs    →  valores reais dos tokens de cor e espaçamento
         ↓
Claude analisa:
  → contraste texto/fundo (WCAG AA: 4.5:1 para texto normal, 3:1 para texto grande)
  → tamanho mínimo de área de toque em elementos interativos (44x44px)
  → hierarquia de headings (H1 > H2 > H3 sem pulos)
  → elementos sem label textual acessível (ícones, imagens, botões só com ícone)
  → foco visível em campos e botões
         ↓
Claude gera o relatório:
  → score por categoria (contraste, toque, semântica, labels)
  → lista de problemas com severidade (crítico / alerta / sugestão)
  → recomendação de correção para cada item
         ↓
Ação opcional:
  use_figma            →  corrige valores de cor para passar no contraste
  Figma /comments      →  posta comentários nos layers com problema
```

## Como ficaria na prática

```
Você:
  Faz um review de acessibilidade nessa tela de login:
  https://figma.com/design/XYZ.../login?node-id=42-100

Claude:
  ## Review de Acessibilidade — Tela de Login
  Score geral: 61% | 7 problemas encontrados

  **Críticos (3)**
  - "Label do campo Email" usa color.neutral.400 sobre branco → contraste 2.6:1
    Mínimo AA: 4.5:1 | Correção: usar color.neutral.700 (#595959)
  - Botão "Entrar" tem área de toque de 36x36px
    Mínimo: 44x44px | Correção: aumentar padding vertical para 12px
  - Ícone de "mostrar senha" sem label acessível
    Correção: adicionar anotação de aria-label="Mostrar senha" no handoff

  **Alertas (2)**
  - Link "Esqueci minha senha" usa apenas cor para indicar que é clicável
    Recomendação: adicionar sublinhado ou outro indicador não-cromático
  - Placeholder do campo Senha usado como label
    Recomendação: adicionar label visível acima do campo

  **Sugestões (2)**
  - Foco visível do teclado não está desenhado nos campos
    Sugestão: adicionar frame de estado focus com outline de 2px
  - Mensagem de erro usa apenas ícone vermelho sem texto explicativo

  Quer que eu corrija os itens críticos de cor agora?
```

## O que o agente resolve bem

- Verifica contraste calculando a relação real entre os tokens — não é estimativa visual
- Detecta problemas que ficam fora do radar em reviews manuais (área de toque, labels de ícones)
- Separa o que é crítico (bloqueia acessibilidade) do que é melhoria (melhora experiência)

## Limitações

- A análise de hierarquia semântica (H1/H2) é baseada nos nomes e estilos dos layers — depende de nomenclatura consistente no Figma
- Análise de foco visível e estados de teclado exige que esses estados estejam desenhados em frames separados
- Correção automática de cores de tokens exige Full seat
