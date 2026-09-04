---
slug: edital-quest
tipo: jogo
status: ativa
regime: provisório (revisão obrigatória no marco "primeira questão funcionando como combate, ponta a ponta, em celular real")
criado: 2026-08-27
atualizado: 2026-08-27
---

# BLUEPRINT — Edital Quest

> **Fase 4.** Arquitetura física e organograma, submetidos à 2ª assinatura do CEO.
> Base: `edital-quest.md` (GDD aprovado em 2026-08-27, com emenda D3-a).
> **Nenhum arquivo do projeto existe até esta assinatura.**

## Regime: **provisório**

Justificativa (R-07): stack nova para o CEO — Phaser 4, React 19, agendador SRS, IndexedDB. A árvore **vai estar errada em algum ponto**, e isso não é fracasso do blueprint: é o limite honesto do que se sabe antes de compilar.

O regime provisório muda três coisas:
1. **Revisão agendada** no marco *"primeira questão funcionando como combate, ponta a ponta, rodando em celular real"*. Chegou o marco, revisamos — mesmo que nada pareça errado.
2. **Estrutura mais rasa.** Menos subpastas antecipadas; divide-se quando a dor aparecer.
3. **Equipe menor.** Quatro agentes, não seis.

Fora do marco, alterar a árvore continua sendo gatilho de bloqueio.

---

# SEÇÃO 1 — Arquitetura Física

```
edital-quest/
├── .claude/                 ← configuração da equipe; gerada no boot, ninguém edita depois
│   ├── agents/              ← um .md por agente, transcrito da Seção 2
│   ├── hooks/               ← territorio.json + territorio.ps1 (enforcement executável)
│   └── settings.json        ← permissões deny + registro do hook
├── docs/                    ← fonte da verdade documental
│   ├── GDD.md               ← congelado, bloqueado por permissão
│   ├── BLUEPRINT.md         ← congelado, bloqueado por permissão
│   ├── CHANGELOG-ESCOPO.md  ← única porta de entrada para mudança de escopo
│   └── ADR/                 ← decisões técnicas estruturais
├── assets/                  ← ORIGEM editável: mapas Tiled, sprites, atlas da fonte, charset
├── public/                  ← servido cru pelo Vite: manifest PWA, ícones, favicon
├── src/
│   ├── contracts/           ★ FRONTEIRA DE CONTRATO — tipos compartilhados, dono único
│   ├── game/                ← Phaser: cenas, combate, render (a rota /jogo)
│   ├── srs/                 ← agendador de repetição espaçada
│   ├── ui/                  ← camada de leitura em DOM sobre o canvas
│   ├── pages/               ← páginas React fora do jogo
│   ├── data/                ← persistência IndexedDB
│   ├── content/             ← questões curadas e tabelas de balanceamento
│   ├── styles/              ← CSS global, incluindo a cascata image-rendering
│   ├── App.tsx              ← rotas
│   └── main.tsx             ← entrada
├── tests/                   ← toda a suíte
├── index.html
├── vite.config.ts
├── tsconfig.json
├── package.json
└── .gitignore
```

## Justificativa por diretório

| Diretório | Por que existe **nesta stack** |
|---|---|
| `assets/` | Tiled produz `.tmx` editável e exporta `.json`; a fonte bitmap precisa de `.png` + `.xml` gerados a partir de um charset autorado. São **artefatos de origem**, distintos do que o navegador carrega. Separados de `public/` porque têm dono diferente e ciclo de vida diferente. |
| `public/` | Vite copia cru para a raiz do build. Serve **apenas** o shell PWA — manifest, ícones. Não guarda asset de jogo (ver decisão D-2 abaixo). |
| `src/contracts/` | **A razão de este projeto conseguir paralelismo.** Três agentes precisam concordar sobre o formato de uma questão, o estado do agendador e os eventos entre React e Phaser. Sem um lugar único e com dono único, esses tipos se duplicam e divergem. |
| `src/game/` | Phaser vive numa rota só, carregado sob demanda (`import('phaser')`). Isolar aqui torna trivial o *lazy loading* dos 356 KB e o `game.destroy(true)` ao sair da rota. |
| `src/srs/` | O agendador **não é lógica de jogo** — é a regra pedagógica do produto. Separado porque tem ciclo de teste próprio (determinístico, sem canvas) e porque é o candidato mais provável a virar território próprio na revisão do marco. |
| `src/ui/` | A camada de leitura em DOM (enunciado, alternativas, artigo de lei). Separada de `pages/` porque **renderiza sobre o canvas** e compartilha o contexto do jogo — restrição da WCAG 1.4.4, decidida no GDD §4.1. |
| `src/pages/` | React comum, sem canvas: menu, estatísticas, configurações. |
| `src/data/` | IndexedDB. Isolado porque no MVP é local e na v1.1 vira sincronização — trocar a implementação sem tocar em quem consome. |
| `src/content/` | As 40 questões curadas e as tabelas de balanceamento. **Conteúdo é design neste projeto**, então tem dono de design, não de engenharia. |
| `src/styles/` | A cascata `image-rendering` e o `100svh` são requisitos de renderização documentados na pesquisa A.3 — precisam de lugar explícito, não espalhados. |
| `tests/` | Fora de `src/` para que o território do `qa` não colida com o de ninguém. |
| `docs/ADR/` | Regime provisório significa decisões que serão revisitadas. Sem ADR, ninguém lembra por que a escolha foi feita quando chegar a hora de mudá-la. |

## Fronteiras de contrato

`src/contracts/` é território compartilhado com **dono único: `engine-dev`**. Três arquivos:

| Arquivo | Define | Quem consome |
|---|---|---|
| `question.ts` | formato de uma questão curada: enunciado, alternativas, gabarito, artigo de lei, bloco temático, dificuldade, e a **procedência** (banca, concurso, ano, cargo — requisito da permissão do Cebraspe) | `game-designer` (produz conforme), `engine-dev` (combate), `frontend-dev` (renderiza) |
| `srs.ts` | estado do agendador por par (usuário, questão): `easiness`, `interval`, `repetitions`, `dueDate`, `lapses` | `engine-dev` (calcula), `frontend-dev` (persiste) |
| `events.ts` | nomes e payloads do `EventBus` — a ponte React ↔ Phaser | `engine-dev` (emite), `frontend-dev` (escuta) |

**Alterar qualquer arquivo de `src/contracts/` é gatilho de bloqueio para todos os agentes, inclusive o dono.** Mudança de contrato quebra trabalho paralelo em andamento; sobe ao CEO com o impacto declarado.

## Decisões de arquitetura registradas

**D-1 — `src/` não tem dono.** Apenas suas subpastas têm. Isso evita que um território aninhe dentro de outro, o que quebraria o hook de enforcement (que casa por prefixo).

**D-2 — Assets de jogo entram por `import ... ?url`, não por `public/`.** Colocar mapas e sprites em `public/` faria `public/` e `public/game/` terem donos diferentes — prefixos aninhados, exatamente o que o enforcement não resolve. Importar de `assets/` com o sufixo `?url` do Vite entrega uma URL versionada, mantém `assets/` como pasta de primeiro nível com dono único, e ainda ganha *cache busting*. Decisão de arquitetura, não de gosto.

**D-3 — Documentos assinados são bloqueados por permissão, não por convenção.** `docs/GDD.md` e `docs/BLUEPRINT.md` entram no `deny` do `settings.json`. O `game-designer` é dono de `docs/`, mas o `deny` tem precedência sobre território — ele escreve no CHANGELOG e nos ADRs, nunca nos documentos assinados. Isso torna as duas assinaturas do CEO **estruturalmente irreversíveis de dentro da oficina**.

**D-4 — Configuração de build pertence ao `frontend-dev`.** `vite.config.ts`, `tsconfig.json`, `package.json`, `index.html`. Adicionar dependência continua sendo gatilho de bloqueio universal — o dono controla o arquivo, não a decisão.

**D-5 — Arte e áudio de pacote pronto, com licença clara (CEO, 2026-08-27).** Nada de pixel art autoral no MVP. *"Perder tempo desenhando sprites nesta fase é o caminho mais rápido para matar o projeto antes de validar o loop."* Arte própria só depois da fatia vertical validada.

Isso cria uma **obrigação de procedência simétrica à das questões**:

- `assets/CREDITOS.md` — arquivo obrigatório, dono `game-designer`. Uma linha por pacote: **nome, autor, URL, licença, data de obtenção**.
- Licenças aceitas sem fricção: **CC0** e domínio público. **CC-BY é aceitável mas exige atribuição visível no jogo** — se entrar CC-BY, a tela de créditos deixa de ser opcional no MVP.
- **Licença não-comercial (NC) é proibida**, mesmo que o MVP não cobre — o produto pretende cobrar depois, e trocar arte no meio é retrabalho garantido.

**Risco de coerência visual, registrado:** pacotes gratuitos misturados produzem jogo que parece colcha de retalhos — paletas, tamanhos de tile e alturas de sprite não combinam entre autores. **Recomendação:** escolher **um** pacote base que resolva mapa e personagens, e complementar pontualmente. Um pacote medíocre e coerente vence três excelentes e incompatíveis.

## Não-versionado (`.gitignore`)

```
node_modules/
dist/
dist-ssr/
coverage/
.vite/
*.local
.env
.env.*
.DS_Store
```

Fonte de arte editável **é versionada** (`assets/`) — perder o `.tmx` original custa mais que o espaço que ele ocupa.

---

# SEÇÃO 2 — Organograma

**Quatro agentes.** Regime provisório pede equipe enxuta; quatro é o mínimo que cobre design, motor, interface e verificação sem que ninguém acumule dois papéis conflitantes.

## 2.1 `game-designer` — *Game Designer e Curador*

**Área de atuação (escrita livre):**
- `docs/CHANGELOG-ESCOPO.md`, `docs/ADR/`
- `assets/`
- `src/content/`

**Gatilhos de bloqueio** — os seis universais, mais:
7. Mudança que afete o **pilar** (*"aprender é jogar; a questão é o combate"*) ou a fantasia do GDD §1.
8. Passar de **40 questões**, ou de **1 matéria**, ou de **4 tipos de inimigo** — são números do MVP.
9. Questão sem procedência completa (banca, concurso, ano, cargo), ou de banca que **não seja o Cebraspe** — emenda D3-a.
10. Qualquer questão cuja origem não seja verificável na API pública do Cebraspe. **Questão inventada é proibição absoluta, não preferência.**
11. Alterar `src/contracts/question.ts` para acomodar um conteúdo — devolve ao `engine-dev`, não contorna.
12. Asset entrando em `assets/` **sem linha correspondente em `assets/CREDITOS.md`** (nome, autor, URL, licença, data), ou sob licença **não-comercial** — decisão D-5.

**Interface de comunicação:**
- **Entrega para** `engine-dev` → especificação de sistema em `docs/`, e questões em `src/content/` conformes a `src/contracts/question.ts`
- **Entrega para** `frontend-dev` → texto de feedback e artigo de lei, no mesmo formato
- **Recebe de** `qa` → relatório de balanceamento observado e sensação de jogo

---

## 2.2 `engine-dev` — *Desenvolvedor de Motor e Agendador*

**Área de atuação (escrita livre):**
- `src/contracts/` ★ **dono da fronteira**
- `src/game/`
- `src/srs/`

**Gatilhos de bloqueio** — os seis universais, mais:
7. **Alterar `src/contracts/`** — mesmo sendo o dono. Contrato quebrado interrompe trabalho paralelo; sobe com o impacto declarado.
8. Implementação que exija mudar a regra especificada pelo `game-designer` — devolve a especificação, não a reinterpreta.
9. Qualquer coisa que faça **recombate livre alterar o estado do SRS**. A muralha do agendador (GDD §3.1) é inegociável: só campanha com matéria nova e Catacumba agendada mudam agendamento.
10. Trocar o algoritmo do agendador (SM-2 → FSRS) — exige ADR.
11. Escrever texto de leitura longa dentro do canvas. A camada de leitura é DOM, por exigência da WCAG 1.4.4 (GDD §4.1).

**Interface de comunicação:**
- **Recebe de** `game-designer` → especificação de sistema e conteúdo
- **Entrega para** `frontend-dev` → `src/contracts/events.ts` estável e os eventos emitidos
- **Entrega para** `qa` → build rodando com o critério de pronto declarado

---

## 2.3 `frontend-dev` — *Desenvolvedor de Interface e Persistência*

**Área de atuação (escrita livre):**
- `src/ui/`, `src/pages/`, `src/data/`, `src/styles/`
- `src/App.tsx`, `src/main.tsx`
- `public/`
- `index.html`, `vite.config.ts`, `tsconfig.json`, `package.json`

**Gatilhos de bloqueio** — os seis universais, mais:
7. Dependência nova — **inclusive biblioteca de UI, ícones ou fonte** (é dependência **e** decisão de identidade visual).
8. Contrato de eventos não atende a tela — devolve ao `engine-dev`, não contorna no cliente.
9. Qualquer coisa que introduza **conta, login ou dado pessoal**. O MVP é local por decisão de escopo; conta traz LGPD e exige `security-reviewer`, que não existe nesta equipe.
10. Implementar **streak** ou **notificação push** — fora do MVP por causa do Decreto 12.880 art. 9º, §único, III e IV.
11. Alterar a receita de escala inteira ou a cascata `image-rendering` de `src/styles/` sem medir em celular real.

**Interface de comunicação:**
- **Recebe de** `engine-dev` → `src/contracts/events.ts`
- **Recebe de** `game-designer` → texto de feedback e artigo de lei
- **Entrega para** `qa` → telas funcionando com o critério declarado

---

## 2.4 `qa` — *Responsável por Qualidade*

**Área de atuação (escrita livre):**
- `tests/`

**Gatilhos de bloqueio** — os seis universais, mais:
7. Um critério de pronto **não pode ser verificado** como está escrito — devolve ao autor.
8. Falha que exija mudança de design ou arquitetura para corrigir.
9. **Os dois critérios de produto** (5 dias seguidos por vontade própria; acerto na revisão de 10 dias maior que no não revisado) não podem ser medidos com os dados coletados — isso é falha de instrumentação e sobe imediatamente.

**Interface de comunicação:**
- **Recebe de** todos → build ou feature declarada concluída
- **Entrega para** o dono do território → relatório reproduzível: passos, esperado, obtido
- **Entrega para** o CEO → veredito sobre os critérios de pronto

**Restrição estrutural:** o `qa` **não corrige o código que falhou**. Reporta; quem tem o território corrige. É a fronteira mais importante deste papel.

---

## 2.5 Matriz de território

Todo caminho tem dono único. Nenhum aninhamento.

| Caminho | Dono |
|---|---|
| `docs/CHANGELOG-ESCOPO.md`, `docs/ADR/` | `game-designer` |
| `assets/` | `game-designer` |
| `src/content/` | `game-designer` |
| `src/contracts/` | `engine-dev` ★ fronteira |
| `src/game/` | `engine-dev` |
| `src/srs/` | `engine-dev` |
| `src/ui/`, `src/pages/`, `src/data/`, `src/styles/` | `frontend-dev` |
| `src/App.tsx`, `src/main.tsx` | `frontend-dev` |
| `public/` | `frontend-dev` |
| `index.html`, `vite.config.ts`, `tsconfig.json`, `package.json` | `frontend-dev` |
| `tests/` | `qa` |
| `docs/GDD.md`, `docs/BLUEPRINT.md` | **ninguém** — `deny` de permissão |
| `.claude/` | **ninguém** — gerado no boot pelo Diretor |
| `src/` (a pasta em si) | **ninguém** — só subpastas têm dono (D-1) |

Convertida em `territorio.json` no passo 7 do boot.

## 2.6 Fluxo de entrega de uma feature típica

```
1. game-designer   escreve a especificação em docs/ e as questões em src/content/,
                   conformes a src/contracts/question.ts
        ↓
2. engine-dev      implementa o sistema em src/game/ (ou a regra em src/srs/)
                   e emite os eventos de src/contracts/events.ts
        ↓
3. frontend-dev    constrói a camada de leitura em src/ui/ consumindo os mesmos
                   eventos, e persiste o estado por src/data/
        ↓
4. qa              escreve o teste em tests/ contra o critério de aceite,
                   verifica que ele falha antes da correção existir, e reporta
```

Os passos 2 e 3 correm **em paralelo** assim que `src/contracts/events.ts` estabiliza. É exatamente isso que a fronteira de contrato compra.

## 2.7 Quem NÃO existe nesta equipe, e por quê

| Papel ausente | Motivo |
|---|---|
| `platform-specialist` | A plataforma é o navegador, não hardware restrito. As armadilhas de renderização estão documentadas no GDD §5 e cabem ao `frontend-dev`. |
| `backend-dev` / `devops` | O MVP não tem servidor. Persistência é local; entrega é build estático. |
| `security-reviewer` | **Não há dado pessoal enquanto não houver conta.** Torna-se **obrigatório na v1.1**, junto com contas e sincronização — é adição agendada, não omissão. |
| `asset-pipeline` | Volume de assets do MVP (1 mapa, 4 inimigos, 1 chefe, 1 fonte) não justifica agente dedicado. Se a segunda matéria multiplicar os mapas, revisar. |

---

# Submissão ao CEO

## Parecer — onde esta estrutura pode envelhecer mal

**1. `src/srs/` sob o `engine-dev` é o candidato mais provável a se soltar.** Hoje o agendador é pequeno — SM-2 tem cinco campos. Quando entrar otimização de FSRS, análise de retenção e o painel, ele vira um produto dentro do produto, com ciclo de teste e vocabulário próprios. **Gatilho de revisão:** quando `src/srs/` passar de ~400 linhas ou ganhar teste que precise de dados sintéticos de meses.

**2. `src/content/` como arquivos TypeScript aguenta 40 questões, não 500.** Na escala do produto completo, isso quer banco de dados e ferramenta de curadoria — o que é um projeto próprio. **Gatilho:** segunda matéria, ou passar de ~150 questões.

**3. A coerência visual do pacote pronto (D-5) não tem dono técnico.** Sem `asset-pipeline`, quem julga se três pacotes combinam é o `game-designer` — e julgamento estético disperso é como o jogo fica feio sem ninguém decidir que ficaria. **Gatilho:** segundo pacote entrando em `assets/`, ou primeiro asset que precise de conversão além de recorte.

## Decisões do CEO — resolvidas em 2026-08-27

**1. Arte e áudio → pacote pronto com licença clara.** ✅ Registrado como decisão **D-5** na Seção 1, com a obrigação de `assets/CREDITOS.md`, proibição de licença não-comercial, e o alerta de coerência visual. Arte autoral só após a fatia vertical validada.

**2. Equipe → quatro agentes.** ✅ Mantido. Razão do CEO, registrada porque é boa: *"O QA é indispensável para testar se a D5 realmente funciona na prática, sem vieses de quem escreveu o código. A restrição dele a `tests/` e a proibição de alterar código-fonte garante que ele seja um juiz imparcial."*

Isso reforça o §2.4: a proibição de corrigir código não é desconfiança do agente — é o que torna o veredito dele **independente**. Quem conserta não pode ser quem julga se está consertado.

**Nenhuma decisão pendente. O blueprint está completo e pronto para a 2ª assinatura.**

---

*Blueprint submetido em: 2026-08-27*
*Assinado pelo CEO em: **2026-08-27***
*Boot realizado em: **2026-08-27** - hook de territorio validado 12/12*
