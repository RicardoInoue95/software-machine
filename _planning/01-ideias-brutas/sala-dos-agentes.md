---
slug: sala-dos-agentes
tipo: software
status: bruta
criado: 2026-08-25
atualizado: 2026-08-25
---

# Sala dos Agentes

## O estalo (1 frase)
Uma tela onde eu **vejo** os agentes da fábrica trabalhando — estilo Habbo Hotel: cada agente é um personagem numa sala, e dá para ver quem está fazendo o quê, agora.

## De onde veio
Vontade de acompanhar o trabalho dos agentes de forma tangível, em vez de ler log de terminal. Referência estética explícita do CEO: Habbo — sala isométrica, personagens, presença.

## Para quem
CEO (usuário único, uso interno). Um segundo perfil — "mostrar para outras pessoas" — está fora até decisão contrária.

## Por que agora
Os agentes ainda não existem (nenhum projeto bootado). Isso é ao mesmo tempo o **argumento contra** (não há o que observar) e o **argumento a favor** (dá para instrumentar desde o primeiro projeto, sem retrofit).

---

## Pesquisa preliminar (levantada em 2026-08-25)

### O que já existe pronto, sem construir nada

**`claude agents`** — visão de terminal em tabela, com todas as sessões em segundo plano agrupadas por estado (Working / Needs input / Ready for review / Idle). Cada linha traz nome, resumo de uma linha do que o agente está fazendo, e idade. Teclas: `Espaço` espia a saída recente, `Enter` abre o transcript completo, `Ctrl+T` fixa a sessão.

Comandos irmãos: `claude attach <id>`, `claude logs <id>`, `claude agents --json`.

**Isto resolve boa parte da dor hoje, custo zero.** Testar antes de construir qualquer coisa.

### O que NÃO existe

- Painel web embutido
- Endpoint REST de status para consultar
- Qualquer interface visual/gráfica — a visão nativa é só a tabela de terminal

Ou seja: **a sala isométrica precisa ser construída do zero.**

### Fontes de dados disponíveis para construir

| Fonte | O que dá | Risco |
|---|---|---|
| **Hooks** (recomendada) | `SubagentStart` e `SubagentStop` existem, ambos com `agent_id` + `agent_type`; `SubagentStop` traz `last_assistant_message`. Eventos de ferramenta (`PreToolUse`/`PostToolUse`) também carregam identidade do subagente quando disparados dentro de um. São ~30 eventos no total. | Baixo — contrato estável e documentado |
| **Transcripts JSONL** | `~/.claude/projects/<projeto>/subagents/agent-<id>.jsonl`, um arquivo por subagente, tailável ao vivo | **Alto** — formato interno, muda entre versões |
| **OpenTelemetry** | `CLAUDE_CODE_ENABLE_TELEMETRY=1` + exportador OTLP. Métricas de custo, tokens, linhas, tempo ativo; eventos de prompt/resposta/tool | Médio — exige coletor (Grafana/Jaeger/etc.), infra desproporcional para uso de uma pessoa |

**Arquitetura candidata:** hooks de `SubagentStart` / `PostToolUse` / `SubagentStop` escrevem JSONL num log de eventos local → página lê o log → sala isométrica renderiza um personagem por `agent_type` com estado e última ação.

Fatia vertical plausível: **um agente, um estado, atualizando na tela.** Se isso funcionar, o resto é conteúdo visual.

*Nota sobre o campo de resultado do `PostToolUse`: a documentação não detalha o nome exato do campo que carrega a saída da ferramenta, e não há tempo nem contagem de tokens no payload dos hooks. Verificar empiricamente antes de depender disso.*

---

## Perguntas para maturação (preenchido pelo Diretor Geral)

1. **Acompanhar ou auditar?** Ver o que acontece **agora** (tempo real, estado momentâneo) e revisar **o que aconteceu** (histórico, linha do tempo) são produtos diferentes, com arquiteturas diferentes. Qual dos dois é o MVP?

2. **Ambiente ou densidade?** A estética Habbo entrega *presença* — a sensação de uma equipe trabalhando — mas mostra **menos informação por pixel** que uma tabela. Se a resposta for "quero as duas", saiba que a sala custa muito mais e informa menos. O que você quer de verdade?

3. **`claude agents` resolve?** Rode a visão nativa numa sessão com subagentes e responda com honestidade: o que falta nela? Se a resposta for *"nada, só queria que fosse bonito"*, isso é uma resposta legítima — mas muda o projeto de "ferramenta" para "prazer estético", e o escopo deve refletir isso.

4. **Um projeto ou a fábrica inteira?** Observar os agentes de **um** repositório é bem mais simples que agregar várias sessões de projetos diferentes numa sala só.

5. **Quando isso é construído?** Hoje não há agentes rodando — o primeiro projeto ainda não bootou. Construir o observador antes do observado significa mais uma camada de infraestrutura antes do jogo. Isso é aceitável para você, ou o painel espera o primeiro projeto existir?

---

## Notas soltas

- Um **spike** responde a pergunta 3 em uma tarde: rodar `claude agents` com subagentes ativos e ver se a dor some.
- Se virar projeto, é `tipo: software`, e provavelmente com blueprint **provisório** — a arquitetura depende de fatos sobre payload de hook que só o código revela.
- Cuidado com o efeito espelho: um painel que observa a fábrica é infraestrutura sobre infraestrutura. O valor precisa ser real, não estrutural.
