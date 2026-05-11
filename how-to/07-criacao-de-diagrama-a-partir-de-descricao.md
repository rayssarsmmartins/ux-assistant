# 07 — Criação de diagrama a partir de descrição

Você precisa documentar um fluxo de usuário, um mapa de jornada ou um diagrama de estados — mas não quer montar no FigJam do zero. Você descreve o fluxo em linguagem natural e o agente cria o diagrama diretamente no FigJam.

## Fluxo técnico

```
Você:  "cria um fluxo de recuperação de senha com esses passos: ..."
         ↓
Claude interpreta a descrição:
  → identifica estados, decisões e transições
  → organiza em estrutura de diagrama (nós + conexões)
         ↓
Claude cria o diagrama no FigJam:
  generate_diagram     →  gera diagrama a partir de Mermaid ou linguagem natural
                          (flowchart, sequência, estados, jornada)
         ↓
Claude valida:
  get_figjam           →  lê o diagrama criado para confirmar estrutura
  get_screenshot       →  screenshot para você revisar visualmente
```

## Como ficaria na prática

```
Você:
  Cria um fluxo de recuperação de senha no FigJam com esses passos:
  1. Usuário clica em "Esqueci minha senha" na tela de login
  2. Sistema pede o email cadastrado
  3. Se email existe: envia link de redefinição
  4. Se email não existe: mostra erro inline
  5. Usuário acessa link → vai para tela de nova senha
  6. Se senha válida: redireciona para o login com mensagem de sucesso
  7. Se senha inválida (fraca ou não confere): mostra erro e mantém na tela

Claude:
  → interpretei 7 estados com 2 pontos de decisão
  → criando diagrama no FigJam do projeto...
  [screenshot do diagrama]

  Diagrama criado com:
  - Fluxo principal: Login → Email → Link enviado → Nova senha → Sucesso
  - Caminho alternativo 1: email não encontrado → erro inline → retry
  - Caminho alternativo 2: senha fraca → erro → retry

  Quer ajustar algum caminho ou adicionar um estado de expiração do link?

Você:
  Adiciona um estado de "link expirado" após 24h com opção de reenviar

Claude:
  → adicionado nó "Link expirado (>24h)" com saída para "Reenviar link"
  [screenshot atualizado]
```

## O que o agente resolve bem

- Transforma descrição de texto em diagrama visual sem saber sintaxe do FigJam
- Detecta caminhos alternativos implícitos na descrição ("se X, então Y")
- Permite iterar no mesmo chat — adicionar estados, renomear, mudar conexões

## Limitações

- `generate_diagram` exige **Full seat** no Figma
- Diagramas muito complexos (mais de 20 nós) podem perder clareza visual — quebre em sub-fluxos
- O estilo visual do diagrama gerado é padrão do FigJam — customização de cores e layout exige ajuste manual ou chamada adicional de `use_figma`
