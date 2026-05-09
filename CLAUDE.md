# Atelier — IA: leia só isto

## O que é este repositório

Arquivos de configuração do **Atelier** — time virtual de desenvolvimento que roda no Claude Code dentro de cada projeto.

Este repositório não tem código de produto. Tem os `.md` do squad, os templates e o BOOTSTRAP que são clonados para dentro de cada projeto ao iniciar.

## Estrutura

```
squad/
├── BOOTSTRAP.md          ← lido pelo Claude Code ao iniciar um projeto
├── PM.md                 ← coordenação, sprints, roadmap
├── ANALISTA.md           ← quebra de requisitos, roteamento
├── DESENVOLVEDOR.md      ← único que edita código-fonte
├── TECH_LEAD.md          ← revisão técnica e decisões de arquitetura
├── QA.md                 ← validação de critérios de aceite
├── DEVOPS.md             ← infra, custos, deploy (sob demanda)
├── DESIGNER.md           ← UX/UI (sob demanda)
├── RH.md                 ← cria experts de domínio (episódico)
└── templates/
    ├── CLAUDE.md.template
    ├── SPRINT_ATELIER.md.template
    ├── ROADMAP.md.template
    └── CHANGELOG.md.template
```

## Como editar

Cada `.md` é o system prompt de um cargo. Edite diretamente — sem build, sem dependências.

**Para melhorar um cargo:** edite o `.md` correspondente e faça commit. Projetos existentes precisam fazer `git pull` dentro de `.atelier/squad/` para pegar a versão atualizada.

**Para adicionar um cargo:** crie um novo `.md` em `squad/` e atualize o `BOOTSTRAP.md` para mencioná-lo na tabela de cargos disponíveis.

**Para ajustar os templates:** edite os arquivos em `squad/templates/`. Afeta apenas projetos novos — projetos existentes já têm os arquivos gerados.

**Para ajustar o bootstrap:** edite o `BOOTSTRAP.md`. Afeta o comportamento do `start project` e do `migrar para Atelier` em projetos futuros.

## Como os projetos usam este repositório

Ao rodar `start project` ou `migrar para Atelier` em um projeto, o Claude Code clona este repositório para `.atelier/squad/` dentro do projeto. A partir daí o projeto usa sua própria cópia local — mudanças aqui não afetam projetos já configurados automaticamente.

Para atualizar um projeto existente com melhorias do squad:

```bash
cd .atelier/squad
git pull
```

## Relação com o Quorum

O Quorum gera o briefing que inicia o bootstrap do Atelier. Repositório do Quorum: `https://github.com/lluizllucas/quorum`

## Não há SNAPSHOT aqui

Este repositório não tem estado de sprint nem roadmap de produto. Ele é infraestrutura — muda só quando você decide melhorar algum cargo ou o fluxo de bootstrap.
