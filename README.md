# Atelier

Time virtual de desenvolvimento que executa projetos no Claude Code. É o irmão do [Quorum](https://github.com/seuuser/quorum) — onde o Quorum decide *se* uma ideia vale a pena, o Atelier executa *como* ela vai ser construída.

## O que é

Um conjunto de cargos especializados (PM, Analista, Desenvolvedor, Tech Lead, QA, DevOps, Designer, RH) que atuam sob comando do Claude Code dentro do seu repositório. Cada cargo tem responsabilidade clara, é acionado sob demanda, e o trabalho do time é coordenado por arquivos vivos: `SPRINT_ATELIER.md`, `ROADMAP.md` e `CHANGELOG.md`.

## Como funciona o fluxo completo

```
┌─────────────────────────────────────────────────────────────┐
│  1. QUORUM (Claude.ai)                                      │
│     Análise de viabilidade da ideia                         │
│     Output: BRIEFING_QUORUM.md                              │
└──────────────────────────┬──────────────────────────────────┘
                           │ (passo manual: copiar arquivo)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  2. REPOSITÓRIO VAZIO + BRIEFING_QUORUM.md                  │
│     Você cola o briefing na raiz do repo                    │
└──────────────────────────┬──────────────────────────────────┘
                           │ (start project)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  3. ATELIER (Claude Code)                                   │
│     Lê o briefing → clona squad → cria estrutura .atelier/  │
│     PM coordena sprints, Dev implementa, QA valida          │
└─────────────────────────────────────────────────────────────┘
```

## Setup no Claude Code

### Pré-requisitos
- Repositório git inicializado (pode estar vazio)
- Claude Code instalado
- O arquivo `BRIEFING_QUORUM.md` gerado pelo Quorum, na raiz do repo

### Comando de bootstrap
Na primeira sessão do Claude Code no projeto, digite:

```
start project
```

O Atelier irá:
1. Ler o `BRIEFING_QUORUM.md`
2. Clonar este repositório de squad para `.atelier/squad/`
3. Criar a estrutura inicial em `.atelier/`
4. Adicionar `.atelier/` ao `.gitignore`
5. Apresentar o resumo do projeto e a primeira sprint sugerida

### Comandos do dia a dia

| Comando | Quem responde | O que faz |
|---|---|---|
| `start project` | Bootstrap | Setup inicial do projeto |
| `próxima tarefa` | PM | Indica a próxima tarefa do sprint atual |
| `continuar sprint` | PM | Continua de onde parou |
| `fechar sprint` | PM | Fecha sprint atual e prepara o próximo |
| `revisar [ITEM]` | Tech Lead | Revisão de design/arquitetura antes do dev |
| `validar [ITEM]` | QA | Valida implementação contra critérios de aceite |
| `convocar [DOMÍNIO]` | RH | Cria expert dinâmico para o domínio |
| `/save` | Sistema | Salva log da sessão no vault Obsidian |
| `/resume` | Sistema | Lê últimos logs e resume estado atual |

## Estrutura do squad

```
squad/
├── BOOTSTRAP.md         ← instruções de inicialização (lidas pelo Code)
├── PM.md                ← Project Manager - dono do SPRINT e ROADMAP
├── ANALISTA.md          ← Analista - quebra requisitos, ponto de roteamento
├── DESENVOLVEDOR.md     ← Único autorizado a editar código
├── TECH_LEAD.md         ← Revisor técnico, questiona decisões de arquitetura
├── QA.md                ← Executa testes, reporta bugs, não edita código
├── DEVOPS.md            ← Sob demanda - infra, CI/CD, deploy
├── DESIGNER.md          ← Sob demanda - UX/UI
├── RH.md                ← Episódico - cria experts de domínio
└── templates/
    ├── CLAUDE.md.template          ← criado em todo projeto
    ├── SPRINT_ATELIER.md.template  ← criado em todo projeto
    ├── ROADMAP.md.template         ← criado em todo projeto
    └── CHANGELOG.md.template       ← criado em todo projeto
```

## Arquivos vivos do projeto

Após o bootstrap, o repositório do projeto terá:

```
seu-projeto/
├── BRIEFING_QUORUM.md    ← contexto inicial (commitado, leitura sob demanda)
├── .gitignore            ← .atelier/ adicionado automaticamente
└── .atelier/             ← gitignored
    ├── CLAUDE.md         ← ponto de entrada do Code
    ├── SPRINT_ATELIER.md ← sprint atual, dono: PM
    ├── ROADMAP.md        ← visão completa, dono: PM
    ├── CHANGELOG.md      ← itens fechados, dono: PM
    └── squad/            ← clonado deste repo
```

## Princípios de operação

**Otimização de tokens.** O Code só lê o `.md` do cargo que está ativo no momento. PM ativo? Só lê `PM.md`. O `BRIEFING_QUORUM.md` só é consultado quando alguém pergunta sobre escopo/visão. No dia a dia, o `SPRINT_ATELIER.md` basta.

**Donos únicos.** O PM é o único que edita `SPRINT_ATELIER.md`, `ROADMAP.md` e `CHANGELOG.md`. O Desenvolvedor é o único que edita o código-fonte. Outros cargos reportam, sugerem, mas não escrevem nesses arquivos.

**Sistema de claims.** Antes de iniciar uma tarefa, o cargo marca `🔒 [CARGO]` no item. Evita conflitos quando há múltiplas sessões.

**Roteamento explícito.** Quando um cargo não sabe o que fazer, ele responde `[CARGO] Não tenho contexto suficiente. Acionando ANALISTA.` O Analista assume e decide o próximo passo.

**Logs persistentes.** Ao fechar sessão, executar `/save` para gerar log no vault Obsidian. O Atelier não tem memória entre sessões — o vault é a memória.

## Atualizando o squad

Os arquivos `.md` deste repositório são versionáveis. Você pode editar prompts, melhorar os cargos, e cada projeto novo (ou cada `git pull` dentro de `.atelier/squad/`) usará a versão atualizada.

Para projetos existentes, basta entrar no `.atelier/squad/` e dar `git pull`.

## Relação com o Quorum

O Quorum é episódico — analisa uma ideia e encerra. O Atelier é contínuo — acompanha um projeto ao longo do tempo. O `BRIEFING_QUORUM.md` é a ponte entre os dois.

---

## Projeto existente — migrar para Atelier

Se o projeto já tem código, `CLAUDE.md` ou `ROADMAP*.md` maduros, use:

```
migrar para Atelier
```

O Atelier irá:
1. Ler o `CLAUDE.md` e `ROADMAP*.md` existentes — sem apagar nada
2. Clonar o squad para `.atelier/squad/`
3. Criar `.atelier/CLAUDE.md` apontando para os arquivos originais
4. Montar o `SPRINT_ATELIER.md` a partir do roadmap existente, preservando siglas e nomenclatura
5. Migrar itens já concluídos para o `CHANGELOG.md`
6. Apresentar a próxima tarefa identificada no SNAPSHOT original

**Regra de ouro:** nada que já existe é apagado, movido ou sobrescrito.

### Para projetos sem BRIEFING_QUORUM.md

O briefing não é obrigatório em migrações. O Atelier extrai o contexto diretamente dos arquivos existentes do projeto.
