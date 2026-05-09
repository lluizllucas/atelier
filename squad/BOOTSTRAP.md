# BOOTSTRAP — Setup do Atelier

## Ativação automática

O Claude Code deve executar este arquivo automaticamente quando encontrar
`BRIEFING_QUORUM.md` na raiz do repositório ao abrir uma sessão nova.

Não é necessário nenhum comando do usuário. O fluxo é:

1. Claude Code abre no diretório do projeto
2. Detecta `BRIEFING_QUORUM.md` na raiz
3. Lê a seção "Instruções para o Claude Code" do briefing
4. Identifica se é projeto novo ou existente
5. Clona o squad e executa o modo correto

Se `.atelier/` já existir, o bootstrap foi executado anteriormente — ignorar e
carregar `.atelier/CLAUDE.md` normalmente.

---


Este arquivo é lido pelo Claude Code quando o usuário digita um dos comandos de bootstrap. Existem dois modos:

- **`start project`** → projeto novo (repo vazio com apenas `BRIEFING_QUORUM.md`)
- **`migrar para Atelier`** → projeto existente (repo com código e arquivos maduros)

Identifique o modo correto antes de executar. Em caso de dúvida, pergunte ao usuário uma única vez.

---

# MODO 1 — `start project` (projeto novo)

## Etapa 1 — Validar pré-requisitos

Verifique:
1. `BRIEFING_QUORUM.md` existe na raiz
2. O diretório é um repositório git
3. `.atelier/` não existe (se existir → abortar, Atelier já configurado)
4. `CLAUDE.md` não existe com conteúdo significativo (se existir → recomendar `migrar para Atelier`)

Se qualquer verificação falhar, pare e informe o usuário.

## Etapa 2 — Ler o briefing

Leia o `BRIEFING_QUORUM.md` completo e extraia:
- Nome do projeto
- Descrição, stack, como testar
- Restrições permanentes e regra absoluta
- Definição de MVP
- Roadmap inicial

## Etapa 3 — Clonar o squad

```bash
mkdir -p .atelier/squad
git clone https://github.com/seuuser/atelier.git .atelier/_squad-temp
cp -r .atelier/_squad-temp/squad/. .atelier/squad/
rm -rf .atelier/_squad-temp
```

## Etapa 4 — Criar arquivos a partir dos templates

Use os templates em `.atelier/squad/templates/` para criar:

- `.atelier/CLAUDE.md`
- `.atelier/SPRINT_ATELIER.md` — PM seleciona 3–5 primeiras tarefas do roadmap
- `.atelier/ROADMAP.md`
- `.atelier/CHANGELOG.md`

Substitua todos os placeholders `{CAMPO}` com os dados do briefing.

## Etapa 5 — Atualizar .gitignore

```
# Atelier — coordenação local
.atelier/
```

O `BRIEFING_QUORUM.md` permanece commitado.

## Etapa 6 — Vault (opcional)

Se `OBSIDIAN_VAULT` estiver definido como variável de ambiente, criar estrutura de pastas do projeto no vault. Caso contrário, registrar no `CLAUDE.md` que `/save` está desabilitado até o usuário configurar o caminho.

## Etapa 7 — Confirmação

```
✅ Atelier configurado: {PROJETO} (projeto novo)

  .atelier/CLAUDE.md
  .atelier/SPRINT_ATELIER.md
  .atelier/ROADMAP.md
  .atelier/CHANGELOG.md
  .atelier/squad/  (8 cargos)

Próximo passo: "próxima tarefa"
```

---

# MODO 2 — `migrar para Atelier` (projeto existente)

Use quando o repo já tem código, `CLAUDE.md`, `ROADMAP*.md` ou histórico de desenvolvimento.

**Regra de ouro: não apagar, não sobrescrever, não mover nada que já existe.**

## Etapa 1 — Detectar estado do projeto

Leia e registre o que encontrar:

```
CLAUDE.md na raiz?        sim/não — [versão/conteúdo resumido]
ROADMAP*.md na raiz?      sim/não — [nome do arquivo encontrado]
BRIEFING_QUORUM.md?       sim/não
Código-fonte presente?    sim/não — [linguagem principal detectada]
.atelier/ já existe?      sim/não — se sim, abortar
```

Se `.atelier/` já existir: abortar e informar que o Atelier já está configurado.

## Etapa 2 — Extrair contexto dos arquivos existentes

**Se `CLAUDE.md` existir na raiz:**
Leia inteiro e extraia:
- Nome do projeto
- Descrição e stack
- Como testar
- Restrições permanentes
- Regra absoluta (buscar em seção de regras ou no SNAPSHOT do ROADMAP)
- Caminho do vault Obsidian (se presente)

**Se `ROADMAP*.md` existir (qualquer nome):**
Leia inteiro e extraia:
- SNAPSHOT (status atual, branch, próxima tarefa, claims, blockers)
- Trilhas e itens ainda abertos (`[ ]`)
- Itens já concluídos (`[x]` ou seção "Já Concluído")
- Perguntas abertas
- Definição de MVP

**Se `BRIEFING_QUORUM.md` não existir:**
Normal — não é obrigatório em projetos existentes. Continue sem ele.

## Etapa 3 — Clonar o squad

Igual ao Modo 1:

```bash
mkdir -p .atelier/squad
git clone https://github.com/seuuser/atelier.git .atelier/_squad-temp
cp -r .atelier/_squad-temp/squad/. .atelier/squad/
rm -rf .atelier/_squad-temp
```

## Etapa 4 — Criar arquivos do Atelier (sem tocar nos existentes)

**`.atelier/CLAUDE.md`**
Crie a partir do template `CLAUDE.md.template`, preenchido com o contexto extraído dos arquivos existentes. Este arquivo é o ponto de entrada do Atelier — aponta para os arquivos originais do projeto quando necessário.

Adicione no `.atelier/CLAUDE.md`:

```markdown
## Arquivos originais do projeto

Este projeto já tinha contexto antes do Atelier. Os arquivos originais são mantidos:

| Arquivo | Função |
|---|---|
| `CLAUDE.md` (raiz) | Contexto técnico completo do projeto |
| `{NOME_ROADMAP}.md` (raiz) | Roadmap completo e SNAPSHOT |

O Atelier usa `.atelier/SPRINT_ATELIER.md` como sprint corrente.
Para contexto técnico profundo, leia `CLAUDE.md` na raiz.
Para visão completa do roadmap, leia `{NOME_ROADMAP}.md` na raiz.
Não duplique informação — referencie, não copie.
```

**`.atelier/SPRINT_ATELIER.md`**
O PM monta o primeiro sprint a partir do roadmap existente:

1. Leia os itens abertos do `ROADMAP*.md`
2. Identifique bloqueantes e próxima tarefa apontada no SNAPSHOT
3. Selecione 3–5 itens para o primeiro sprint, começando pelo que estava marcado como `PRÓXIMA` no SNAPSHOT original
4. Preserve siglas e nomenclatura exatamente como estão no roadmap (ex: `VAL.1`, `CONT.1`)

Formato do SNAPSHOT inicial:

```
SPRINT     : #1 — Migração para Atelier · {DATA_ATUAL}
STATUS     : Migração concluída. Continuando de onde o projeto parou.
BRANCH     : {BRANCH_DO_SNAPSHOT_ORIGINAL}
PRÓXIMA    : {PROXIMA_DO_SNAPSHOT_ORIGINAL}
CLAIMS     : {CLAIMS_DO_SNAPSHOT_ORIGINAL}
BLOCKER    : {BLOCKERS_DO_SNAPSHOT_ORIGINAL}
SESSÃO     : 1G · ou · 2-3M · ou · 4-6P

REGRA ABSOLUTA:
  {REGRA_ABSOLUTA_EXTRAIDA}

QUANDO LER MAIS:
  Contexto técnico completo   →  CLAUDE.md (raiz)
  Roadmap completo            →  {NOME_ROADMAP}.md (raiz)
  Itens fechados              →  .atelier/CHANGELOG.md
```

**`.atelier/CHANGELOG.md`**
Se o roadmap original tiver seção "Já Concluído" ou itens marcados com `[x]`, migre-os para o CHANGELOG com a nota:

```
## Migrado do histórico do projeto em {DATA_ATUAL}

[lista dos itens já concluídos encontrados no roadmap original]
```

**`.atelier/ROADMAP.md`**
**Não duplique** o roadmap existente. Crie um arquivo mínimo que aponta para o original:

```markdown
# ROADMAP — {PROJETO}

O roadmap completo está em `{NOME_ROADMAP}.md` na raiz do repositório.
Este arquivo existe apenas para manter a estrutura do Atelier.

O PM atualiza o roadmap diretamente em `{NOME_ROADMAP}.md`.
```

## Etapa 5 — Atualizar .gitignore

```
# Atelier — coordenação local
.atelier/
```

**Não remova** entradas existentes do `.gitignore`.

## Etapa 6 — Vault (opcional)

Se o `CLAUDE.md` original mencionar caminho do vault Obsidian, use o mesmo caminho no `CLAUDE.md` do Atelier. Não criar nova estrutura de pastas se já existir.

## Etapa 7 — Confirmação

```
✅ Atelier migrado: {PROJETO}

Arquivos originais preservados:
  CLAUDE.md (raiz) — contexto técnico
  {NOME_ROADMAP}.md (raiz) — roadmap completo

Criado em .atelier/:
  CLAUDE.md         ← ponto de entrada do Atelier
  SPRINT_ATELIER.md ← sprint #1 montada com {N} itens
  ROADMAP.md        ← aponta para roadmap original
  CHANGELOG.md      ← {M} itens migrados do histórico
  squad/            ← 8 cargos disponíveis

Próxima tarefa identificada: {PROXIMA_DO_SNAPSHOT}
Digite "próxima tarefa" para o PM iniciar.
```

---

# Notas gerais para o Claude Code

- Execute todas as etapas sem pedir confirmação entre elas.
- Se uma etapa falhar (ex: clone do squad falha por rede), informe o erro e pare. Não improvise.
- Após o bootstrap, **saia do modo bootstrap**. Carregue `.atelier/squad/PM.md` e aguarde o próximo comando.
- O bootstrap é executado uma única vez por projeto. Em sessões subsequentes, o Code lê `.atelier/CLAUDE.md` diretamente.
- **Nunca sobrescreva arquivos existentes** no Modo 2. Se um arquivo do template colidir com algo existente, crie em `.atelier/` e referencie — nunca substitua.
