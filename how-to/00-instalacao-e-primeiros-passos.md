# 00 — Instalação e primeiros passos

Este guia é para quem nunca usou o Claude Code antes. Você não precisa saber usar
terminal — o Claude Code tem um app desktop com interface de chat, igual ao Claude
que você já conhece, mas com superpoderes para trabalhar com Figma.

---

## O que você vai precisar

- Mac ou Windows
- Conta no [claude.ai](https://claude.ai) (plano Pro ou superior)
- Conta no Figma com Dev seat ou Full seat
- A pasta `ux-assistant` salva no seu computador

---

## Passo 1 — Instalar o Claude Code

1. Acesse [claude.ai/code](https://claude.ai/code)
2. Clique em **Download** e escolha Mac ou Windows
3. Abra o arquivo baixado e siga a instalação normal (arraste para Applications no Mac)
4. Abra o Claude Code — ele vai pedir para você fazer login com sua conta claude.ai

Pronto. A interface é um chat, igual ao Claude normal.

---

## Passo 2 — Abrir o projeto ux-assistant

O Claude Code trabalha dentro de uma pasta de projeto. Você precisa apontar para
a pasta `ux-assistant` para que ele reconheça os comandos e configurações.

**No Mac:**
1. Abra o Claude Code
2. Clique em **Open Project** (ou `Cmd + O`)
3. Navegue até a pasta `ux-assistant` e clique em **Abrir**

**No Windows:**
1. Abra o Claude Code
2. Clique em **Open Project** (ou `Ctrl + O`)
3. Navegue até a pasta `ux-assistant` e clique em **Abrir**

Você vai ver o nome do projeto no topo da janela. A partir daqui, o agente já
conhece todos os workflows, componentes do DS e regras do time.

---

## Passo 3 — Configurar o token do Figma

O agente precisa de uma chave de acesso para ler seus arquivos no Figma.

### Gerar o token no Figma

1. Abra o Figma no browser
2. Clique na sua foto de perfil (canto superior direito) → **Settings**
3. Vá em **Security** → role até **Personal access tokens**
4. Clique em **Generate new token**
5. Dê um nome (ex: `ux-assistant`) e selecione os escopos:
   - `file_content:read`
   - `file_comments:read`
   - `file_comments:write`
6. Clique em **Generate** e **copie o token** — ele só aparece uma vez

### Salvar o token no projeto

1. Na pasta `ux-assistant`, localize o arquivo `.env.example`
2. Duplique esse arquivo e renomeie a cópia para `.env` (sem o `.example`)
3. Abra o `.env` em qualquer editor de texto (Bloco de Notas, TextEdit, VS Code)
4. Cole seu token na linha:
   ```
   FIGMA_PAT=cole_seu_token_aqui
   ```
5. Salve o arquivo

> O arquivo `.env` nunca deve ser compartilhado ou enviado para o Git — ele já está
> no `.gitignore` por segurança.

---

## Passo 4 — Conectar o Figma ao Claude Code

Na primeira vez que rodar um workflow, o Claude Code vai pedir para você autorizar
a conexão com o Figma via browser. É só seguir o fluxo de login que aparece.

Para verificar se tudo está funcionando, digite no chat do Claude Code:

```
mcp__figma__whoami
```

Se aparecer seu nome e tipo de conta do Figma, está tudo certo.

---

## Passo 5 — Preencher o contexto do time

Antes de usar os workflows, vale 5 minutos para preencher dois arquivos com os
dados reais do seu time. Isso garante que as specs geradas tenham os nomes certos
de pessoas e componentes.

**`.claude/knowledge/team/people.md`**
Adicione as pessoas do seu time seguindo o formato do arquivo. Exemplo:

```
## Ana Lima
- Role: Senior UX Designer
- Figma handle: @ana.lima
- Slack: @ana.lima
- Area: Checkout
```

**`.claude/knowledge/design-system/components.md`**
Liste os componentes da biblioteca do seu DS. Exemplo:

```
DS/Button     → variants: Primary, Secondary, Ghost, Danger
DS/Input      → variants: Default, Masked, Search
DS/Badge      → variants: Neutral, Promo, Success, Warning, Error
```

---

## Primeiros comandos para testar

Com o projeto aberto no Claude Code, teste esses três comandos em ordem:

### Teste 1 — Verificar a conexão com o Figma

Digite no chat:

```
mcp__figma__whoami
```

Esperado: seu nome de usuário e tipo de seat do Figma.

### Teste 2 — Ler um frame e ver o que o agente entende

Abra qualquer tela no Figma, clique com o botão direito em um frame →
**Copy link to selection** e cole no chat:

```
Lê esse frame e me conta o que tem nele:
https://www.figma.com/design/SEU_ARQUIVO/...?node-id=42-100
```

O agente vai descrever a estrutura, componentes e tokens que encontrou.
Isso confirma que a leitura está funcionando antes de rodar um workflow completo.

### Teste 3 — Rodar o primeiro workflow

Com um frame de tela completa (não um componente isolado), rode:

```
/design-handoff https://www.figma.com/design/SEU_ARQUIVO/...?node-id=42-100
```

O agente vai ler o frame, gerar a spec e apresentar um resumo para você revisar
antes de qualquer publicação. Nada é enviado para o Notion ou Slack sem você confirmar.

---

## O que fazer se algo não funcionar

**"Figma MCP inacessível"**
→ Feche e reabra o Claude Code, tente conectar novamente. Verifique se o `FIGMA_PAT`
está salvo corretamente no `.env`.

**"Não reconhece o comando /design-handoff"**
→ Verifique se a pasta aberta no Claude Code é a `ux-assistant` (não uma pasta pai).
O projeto precisa estar aberto na raiz correta.

**"Token inválido ou expirado"**
→ Gere um novo token no Figma (Passo 3) e atualize o `.env`.

**Dúvidas sobre um workflow específico**
→ Leia o arquivo correspondente em `how-to/` — cada um tem um exemplo de conversa
completo e a lista de limitações conhecidas.

---

## Próximos passos depois da instalação

1. Leia `how-to/01-melhoria-em-tela-existente.md` — o workflow mais usado no dia a dia
2. Preencha `people.md` e `components.md` com os dados do seu time
3. Teste o `/design-system-audit` em um arquivo que você suspeita ter elementos fora do DS
