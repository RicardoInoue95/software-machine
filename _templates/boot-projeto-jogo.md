# MOLDE: Boot de Projeto de Jogo

> Pré-requisitos (**ambos**): `_planning/03-gdds-aprovados/<slug>.md` com `status: aprovada` **e** `<slug>-BLUEPRINT.md` assinado pelo CEO (Fase 4 — ver *Protocolo de Blueprint* no `CLAUDE.md` da raiz).
> Uso: o Diretor Geral executa este molde a partir da raiz. Resultado: `projetos-ativos/<slug>/` pronto para a primeira sessão de trabalho *dentro* da pasta do projeto.
>
> Este molde **executa** o blueprint — não o redesenha. Árvore de diretórios e organograma vêm prontos e assinados. Divergência entre blueprint e execução é erro: pare e escale.

---

## PROCEDIMENTO

1. Ler o GDD aprovado **e o blueprint assinado** por completo.
2. Criar `projetos-ativos/<slug>/` e rodar `git init` dentro dela.
3. Gerar o `CLAUDE.md` do projeto a partir do gabarito abaixo, preenchendo com o GDD e com a matriz de território do blueprint.
4. Criar `.claude/agents/<nome>.md` para cada agente **definido na Seção 2 do blueprint**, partindo dos arquétipos em `_templates/agentes/` (`game-designer`, `engine-dev`, `platform-specialist`, `asset-pipeline`, `qa`) e **adaptando** com stack, caminhos, gatilhos e interfaces reais. Não inventar agentes nem editar limites aqui. Arquétipo copiado cru = agente genérico = pior que agente nenhum.
5. Criar a estrutura de pastas **exatamente como na Seção 1 do blueprint** (as sugestões ao final deste molde servem apenas de referência para *escrever* o blueprint).
6. Copiar o GDD para `docs/GDD.md`, o blueprint para `docs/BLUEPRINT.md`, e criar `docs/CHANGELOG-ESCOPO.md` vazio.
   🔴 **Copiar com `Copy-Item` (byte a byte). Nunca com `Get-Content -Raw` do PowerShell 5.1** — sem BOM ele lê UTF-8 como ANSI e corrompe todos os acentos. Ver *Convenções → Encoding* no `CLAUDE.md` da raiz. Após copiar, **verificar**: `([regex]::Matches([System.IO.File]::ReadAllText($f,[Text.Encoding]::UTF8),'Ã©|Ã£|Ã§|â€”')).Count` deve ser `0`.
7. **Ferrar o território** — aplicar `_templates/enforcement-territorio.md`: gera `.claude/settings.json`, `.claude/hooks/territorio.json` e `.claude/hooks/territorio.ps1` a partir da matriz do blueprint, e **testa o hook** (os dois casos, permitido e negado). É isto que transforma o limite de agente em parede executável.
8. Criar `.gitignore` adequado à stack e fazer o commit inicial: `chore: bootstrap project from approved GDD`.
9. Atualizar `03-gdds-aprovados/<slug>.md` (`status: ativa`, `Boot realizado em`) e a tabela de Portfólio no `CLAUDE.md` raiz.
10. Instruir o CEO: *"Abra o Claude dentro de `projetos-ativos/<slug>/` para começar a trabalhar."*

---

## GABARITO: `CLAUDE.md` do projeto

```markdown
# <NOME DO JOGO>

tipo: jogo | slug: <slug> | status: ativa | plataforma: <GBA / web / PC...> | engine: <toolchain>

## O que é este projeto
<Fantasia em uma frase + emoção dominante + pilar inegociável. Copiar do GDD seção 1.>

## Fonte da verdade
- `docs/GDD.md` — design aprovado. Mudanças de escopo: registrar em `docs/CHANGELOG-ESCOPO.md` antes de codar.
- Este arquivo — regras de trabalho.
- A raiz da Fábrica (`../../CLAUDE.md`) **não** se aplica aqui além das regras de isolamento.

## MVP — fatia vertical
<Copiar do GDD seção 7: o que está dentro, o que está fora, critérios de pronto como checklist.>
- [ ] critério 1
- [ ] critério 2

## Stack e restrições da plataforma
<Toolchain, versões, comandos de build/run/test. Restrições duras (memória, paleta, resolução, input).>

### Comandos
- Build: `<comando>`
- Rodar em emulador: `<comando>`
- Testar: `<comando>`
- Rodar em hardware: `<procedimento>`

## Estrutura de pastas
<Descrever a estrutura escolhida.>

## Equipe de agentes (`.claude/agents/`)
Organograma completo em `docs/BLUEPRINT.md`. Resumo operacional:

| Agente | Território (escrita livre) | Quando acionar |
|--------|----------------------------|----------------|
| game-designer | `docs/`, dados de balanceamento | ... |
| engine-dev | `src/`, `include/` | ... |
| platform-specialist | ... | ... |
| asset-pipeline | `assets/`, `tools/` | ... |
| qa-tester | `tests/` | ... |

**Matriz de território:** todo caminho tem um dono único. Escrita fora do próprio território = gatilho de bloqueio, escala ao CEO.

## Regras de trabalho
- Toda feature começa com uma linha no GDD ou no CHANGELOG-ESCOPO. Sem documento, sem código.
- Testar em emulador antes de cada commit; em hardware real (se aplicável) antes de cada marco.
- Assets seguem o pipeline em `docs/ASSET-PIPELINE.md`; nada entra em `assets/` fora do formato final.
- Commits em inglês, Conventional Commits (`feat:`, `fix:`, `chore:`, `art:`, `audio:`).
- Performance é feature: qualquer sistema novo é medido (frames, VRAM, tamanho de ROM) e registrado.

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

Você é o **<Papel>** do projeto <Nome do Jogo> (<plataforma>, <engine>).

## Sua responsabilidade
<2–4 linhas específicas. Transcrever do blueprint, Seção 2.>

## Você conhece profundamente
<Restrições, APIs, ferramentas, padrões relevantes para o papel. Para platform-specialist de GBA, por ex.: modos de vídeo, VRAM/OAM, DMA, timers, interrupções, limites de paleta, libtonc/Butano.>

## Área de atuação (escrita livre, sem pedir permissão)
<Caminhos/globs exatos do blueprint. Fora daqui você não escreve.>

## Gatilhos de bloqueio — PARE e escale ao CEO
1. Mudança de escopo do MVP definido em `docs/GDD.md`.
2. Alteração da árvore de diretórios aprovada em `docs/BLUEPRINT.md`.
3. Dependência externa nova (lib, toolchain, asset pack, serviço).
4. Necessidade de escrever fora da sua área de atuação.
5. Conflito com outro agente que o contrato escrito não resolve.
6. Dado pessoal, credencial ou custo financeiro não previsto.
7. <Gatilhos específicos deste papel, vindos do blueprint.>

## Interface de comunicação
- **Recebe de:** <agente> → <artefato, em qual caminho>
- **Entrega para:** <agente> → <artefato, em qual caminho>

## Suas regras
- Leia `docs/GDD.md`, `docs/BLUEPRINT.md` e `CLAUDE.md` antes de agir.
- <Regra específica do papel.>
- Reporte ao orquestrador: o que fez, o que testou, o que ficou pendente.

## Você NÃO faz
<Limites claros para evitar sobreposição com outros agentes.>
```

---

## SUGESTÕES DE ESTRUTURA POR STACK

**GBA (devkitARM + libtonc / Butano):**
```
src/            ← código C/C++
include/
assets/
  gfx/          ← sprites e tiles já convertidos
  audio/
  maps/
tools/          ← scripts de conversão (grit, Tiled → bin)
build/          ← (gitignored)
docs/
  GDD.md
  ASSET-PIPELINE.md
  CHANGELOG-ESCOPO.md
Makefile
```

**Godot:**
```
project.godot
scenes/ · scripts/ · assets/ · addons/ · docs/
```

**Web (Phaser/Pixi/canvas):**
```
src/ · public/assets/ · docs/ · package.json · vite.config.*
```
