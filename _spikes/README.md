# _spikes — A Bancada

A trilha rápida da fábrica. Aqui se escreve código **para descobrir alguma coisa**, não para entregar.

Um spike existe para responder **uma pergunta técnica** que nenhuma quantidade de documento responderia. Exemplos legítimos:

- *"Consigo compilar e rodar um "olá mundo" no devkitARM e ver um sprite se mexendo no emulador?"*
- *"Essa biblioteca de calendário aguenta recorrência ou vou ter que escrever na mão?"*
- *"Quanto de ROM sobra depois de carregar um tileset de 256 cores?"*
- *"Esse fluxo de auth funciona no provedor X sem cartão de crédito?"*

## As quatro regras

**1. Spike não vira produto.** O código do spike é **descartável por contrato**. Quando ele responde a pergunta, a resposta vai para a incubadora e o código morre. Se você se pegar "aproveitando" o spike como base do projeto, você não fez um spike — você fez um projeto sem blueprint, e a fábrica perdeu.

**2. Uma pergunta, uma resposta, um prazo.** Todo spike declara a pergunta no `SPIKE.md` e o prazo antes de começar. Estourou o prazo sem resposta, a resposta é *"não sei, e descobrir custa mais do que eu achava"* — o que já é uma resposta valiosa.

**3. Sem fase, sem assinatura, sem agente.** Nenhuma cerimônia. Sem GDD, sem blueprint, sem organograma, sem `.claude/agents/`. É você e o Claude na bancada.

**4. A saída é um documento, não um binário.** O spike termina quando o `SPIKE.md` tem a seção **Resposta** preenchida. Essa resposta alimenta a Fase 1 (nova ficha) ou a Fase 2 (bloco de pesquisa/riscos de uma ideia em maturação).

## Estrutura

```
_spikes/<slug>/
├── SPIKE.md      ← pergunta, prazo, resposta, destino
└── <o que for>   ← código jogado, sem estrutura, sem teste, sem lint
```

Não há molde para o código. É bancada: bagunça é permitida, porque vai tudo para o lixo.

## Ciclo de vida

```
pergunta técnica → _spikes/<slug>/ → resposta no SPIKE.md → alimenta a incubadora
                                                          ↓
                                              código DESCARTADO
```

O `SPIKE.md` sobrevive (é conhecimento). O código, não.

## Faxina

Spike com resposta preenchida há mais de 30 dias: apagar a pasta, manter só o `SPIKE.md` em `_spikes/_respondidos/<slug>.md`. Spike sem resposta há mais de 30 dias: encerrar com `Resposta: abandonado` e o motivo.

---

## Gabarito de `SPIKE.md`

```markdown
---
slug: <slug>
tipo: spike
pergunta-de: <slug da ideia relacionada, ou "avulso">
aberto: AAAA-MM-DD
prazo: AAAA-MM-DD
status: aberto | respondido | abandonado
---

# Spike — <título curto>

## A pergunta
<UMA pergunta, respondível com sim/não ou com um número. Se tem duas perguntas, são dois spikes.>

## Por que não dá para responder no papel
<Uma linha. Se dá para responder lendo a documentação, leia a documentação e não abra spike.>

## Prazo
<Horas ou dias. Curto. Um spike de duas semanas é um projeto disfarçado.>

## O que vou tentar
<3 a 5 passos. Rascunho, não plano.>

## Resposta
<Preenchido ao final. Seja específico: números, mensagens de erro, versões. "Funcionou" não é resposta.>

## Destino
<Uma das opções:
- vira ficha nova em `_planning/01-ideias-brutas/<slug>.md`
- alimenta `_planning/02-maturacao/<slug>/01-pesquisa.md` (ou `03-riscos.md`)
- mata a ideia `<slug>` — e por quê
- não levou a nada>

## Código descartado em
<AAAA-MM-DD, ou "pendente">
```
