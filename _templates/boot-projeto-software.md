# MOLDE: Boot de Projeto de Software

> Pré-requisitos (**ambos**): `_planning/03-gdds-aprovados/<slug>.md` com `status: aprovada` **e** `<slug>-BLUEPRINT.md` assinado pelo CEO (Fase 4 — ver *Protocolo de Blueprint* no `CLAUDE.md` da raiz).
> Uso: o Diretor Geral executa este molde a partir da raiz. Resultado: `projetos-ativos/<slug>/` pronto para a primeira sessão de trabalho *dentro* da pasta do projeto.
>
> Este molde **executa** o blueprint — não o redesenha. Árvore de diretórios e organograma vêm prontos e assinados. Divergência entre blueprint e execução é erro: pare e escale.

---

## PROCEDIMENTO

1. Ler o PRD aprovado **e o blueprint assinado** por completo.
2. Criar `projetos-ativos/<slug>/` e rodar `git init` dentro dela.
3. Gerar o `CLAUDE.md` do projeto a partir do gabarito abaixo, preenchendo com o PRD e com a matriz de território do blueprint.
4. Criar `.claude/agents/<nome>.md` para cada agente **definido na Seção 2 do blueprint**, partindo dos arquétipos em `_templates/agentes/` (`product-owner`, `backend-dev`, `frontend-dev`, `devops`, `qa`, `security-reviewer`) e **adaptando** com stack, caminhos, gatilhos e interfaces reais. Não inventar agentes nem editar limites aqui. Arquétipo copiado cru = agente genérico = pior que agente nenhum.
5. Criar a estrutura de pastas **exatamente como na Seção 1 do blueprint** (as sugestões ao final deste molde servem apenas de referência para *escrever* o blueprint).
6. Copiar o PRD para `docs/PRD.md`, o blueprint para `docs/BLUEPRINT.md`, criar `docs/CHANGELOG-ESCOPO.md` vazio e `docs/ADR/` (Architecture Decision Records) com o `ADR-0001-stack.md` extraído do PRD.
   🔴 **Copiar com `Copy-Item` (byte a byte). Nunca com `Get-Content -Raw` do PowerShell 5.1** — sem BOM ele lê UTF-8 como ANSI e corrompe todos os acentos. Ver *Convenções → Encoding* no `CLAUDE.md` da raiz. Após copiar, **verificar**: `([regex]::Matches([System.IO.File]::ReadAllText($f,[Text.Encoding]::UTF8),'Ã©|Ã£|Ã§|â€”')).Count` deve ser `0`.
7. **Ferrar o território** — aplicar `_templates/enforcement-territorio.md`: gera `.claude/settings.json`, `.claude/hooks/territorio.json` e `.claude/hooks/territorio.ps1` a partir da matriz do blueprint, e **testa o hook** (os dois casos, permitido e negado). É isto que transforma o limite de agente em parede executável.
8. Criar `.gitignore`, `.env.example` e fazer o commit inicial: `chore: bootstrap project from approved PRD`.
9. Atualizar `03-gdds-aprovados/<slug>.md` (`status: ativa`, `Boot realizado em`) e a tabela de Portfólio no `CLAUDE.md` raiz.
10. Instruir o CEO: *"Abra o Claude dentro de `projetos-ativos/<slug>/` para começar a trabalhar."*

---

## GABARITO: `CLAUDE.md` do projeto

```markdown
# <NOME DO PRODUTO>

tipo: software | slug: <slug> | status: ativa | entrega: <web app / API / CLI / mobile...> | stack: <resumo>

## O que é este projeto
<Problema + usuário + proposta de valor em 3 linhas. Copiar do PRD seções 1–2.>

## Fonte da verdade
- `docs/PRD.md` — requisitos aprovados. Mudanças de escopo: registrar em `docs/CHANGELOG-ESCOPO.md` antes de codar.
- `docs/ADR/` — decisões de arquitetura. Toda decisão estrutural nova ganha um ADR.
- Este arquivo — regras de trabalho.
- A raiz da Fábrica (`../../CLAUDE.md`) **não** se aplica aqui além das regras de isolamento.

## MVP
<Copiar do PRD seção 7: fluxo principal, funcionalidades essenciais, fora-de-escopo, critérios de pronto como checklist.>
- [ ] critério 1
- [ ] critério 2

## Stack
<Linguagem, framework, banco, hospedagem, auth, versões. Justificativa resumida (detalhe no ADR-0001).>

### Comandos
- Instalar: `<comando>`
- Rodar dev: `<comando>`
- Testar: `<comando>`
- Lint/format: `<comando>`
- Migrar banco: `<comando>`
- Deploy: `<comando ou procedimento>`

## Modelo de dados
<Entidades centrais do MVP e relações. Diagrama textual simples.>

## Estrutura de pastas
<Descrever a estrutura escolhida.>

## Equipe de agentes (`.claude/agents/`)
Organograma completo em `docs/BLUEPRINT.md`. Resumo operacional:

| Agente | Território (escrita livre) | Quando acionar |
|--------|----------------------------|----------------|
| product-owner | `docs/` | ... |
| backend-dev | `src/server/`, `src/lib/`, migrações | ... |
| frontend-dev | `src/app/`, `src/components/` | ... |
| devops | CI, infra, deploy | ... |
| qa-engineer | `tests/` | ... |
| security-reviewer | *(leitura em tudo; escrita só em pareceres)* | ... |

**Matriz de território:** todo caminho tem um dono único. Escrita fora do próprio território = gatilho de bloqueio, escala ao CEO.

## Regras de trabalho
- Toda feature nasce de um item do PRD ou do CHANGELOG-ESCOPO com critério de aceite escrito.
- Nenhum PR/commit de feature sem teste automatizado cobrindo o critério de aceite.
- Dados pessoais: `security-reviewer` revisa antes do merge. LGPD não é opcional.
- Segredos só em `.env` (gitignored); `.env.example` sempre atualizado.
- Commits em inglês, Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`, `test:`, `refactor:`).
- Decisão estrutural (troca de lib, mudança de schema, integração nova) → ADR antes do código.

## Estado atual
<Atualizar ao fim de cada sessão: o que funciona, o que está em andamento, próximo passo.>
```

---

## GABARITO: `.claude/agents/<nome>.md`

```markdown
---
name: <nome-do-agente>
description: <uma linha: quando este agente deve ser acionado>
tools: Read, Edit, Write, Grep, Glob, Bash
---

Você é o **<Papel>** do projeto <Nome do Produto> (<stack>).

## Sua responsabilidade
<2–4 linhas específicas. Transcrever do blueprint, Seção 2.>

## Você conhece profundamente
<Frameworks, padrões, ferramentas relevantes para o papel. Para security-reviewer, por ex.: OWASP Top 10, LGPD, gestão de segredos, auth/sessões, rate limiting.>

## Área de atuação (escrita livre, sem pedir permissão)
<Caminhos/globs exatos do blueprint. Fora daqui você não escreve.>

## Gatilhos de bloqueio — PARE e escale ao CEO
1. Mudança de escopo do MVP definido em `docs/PRD.md`.
2. Alteração da árvore de diretórios aprovada em `docs/BLUEPRINT.md`.
3. Dependência externa nova (lib, serviço, API paga).
4. Necessidade de escrever fora da sua área de atuação.
5. Conflito com outro agente que o contrato escrito não resolve.
6. Dado pessoal, credencial ou custo financeiro não previsto.
7. Mudança de schema de banco ou de contrato de API já publicado → exige ADR.
8. <Gatilhos específicos deste papel, vindos do blueprint.>

## Interface de comunicação
- **Recebe de:** <agente> → <artefato, em qual caminho>
- **Entrega para:** <agente> → <artefato, em qual caminho>

## Suas regras
- Leia `docs/PRD.md`, `docs/BLUEPRINT.md`, `docs/ADR/` e `CLAUDE.md` antes de agir.
- <Regra específica do papel.>
- Reporte ao orquestrador: o que fez, o que testou, o que ficou pendente.

## Você NÃO faz
<Limites claros para evitar sobreposição com outros agentes.>
```

---

## GABARITO: `docs/ADR/ADR-0001-stack.md`

```markdown
# ADR-0001: Escolha da stack

Data: AAAA-MM-DD · Status: aceito

## Contexto
<Por que uma decisão era necessária; restrições do PRD.>

## Decisão
<Stack escolhida.>

## Alternativas consideradas
- <Alternativa A> — descartada porque ...
- <Alternativa B> — descartada porque ...

## Consequências
<O que fica mais fácil, o que fica mais difícil, o que precisa ser revisitado e quando.>
```

---

## SUGESTÕES DE ESTRUTURA POR STACK

**Web full-stack (Next.js / SvelteKit / Nuxt + Postgres):**
```
src/
  app/ ou routes/     ← páginas e rotas
  components/
  lib/                ← regras de negócio, acesso a dados
  server/             ← API, auth
prisma/ ou drizzle/   ← schema e migrações
tests/
docs/ (PRD.md, ADR/, CHANGELOG-ESCOPO.md)
.env.example
```

**API + front separados (FastAPI/NestJS + React/Vue):**
```
backend/  (src/, tests/, pyproject.toml ou package.json)
frontend/ (src/, tests/, package.json)
docs/
docker-compose.yml
```

**CLI / biblioteca:**
```
src/ · tests/ · docs/ · README.md · pyproject.toml | package.json | Cargo.toml
```

**Mobile (Flutter / React Native / Expo):**
```
lib/ ou src/ · assets/ · test/ · docs/
```
