---
slug: edital-quest
tipo: jogo
status: maturando
criado: 2026-08-27
atualizado: 2026-08-27
---

# Escopo — Edital Quest

## O problema de escopo, dito sem rodeio

O produto completo descrito na ficha contém **quatro produtos**: um RPG, um motor de repetição espaçada, um banco de questões curado e um painel de retenção. Cada um sozinho é um projeto respeitável. Juntos, para uma pessoa, é como o projeto morre.

Este documento corta até doer. O que sobra é a **fatia vertical**: fina em conteúdo, **completa em ciclo** — todos os sistemas essenciais funcionando de ponta a ponta, para uma única matéria.

---

## MVP em uma frase

> Um jogador estuda **Regime Disciplinar da Lei 8.112/90** derrotando inimigos com questões reais de concurso, e volta dias depois porque a revisão agendada o deixa mais forte.

## Por que Regime Disciplinar

Escolha do recorte dentro da Lei 8.112/90 (arts. 116 a 142):

1. **Alta incidência em prova.** Deveres, proibições, penalidades e PAD caem em praticamente todo concurso que cobra a 8.112.
2. **Autocontido.** Dá para dominar sem depender de Provimento, Vacância ou Direitos e Vantagens.
3. **Narrativamente rico.** É a única parte da lei que já é sobre transgressão, apuração e punição — conflito pronto, sem precisar forçar metáfora. Advertência, suspensão e demissão são literalmente uma escala de dano.

*Alternativa descartada: começar por "Provimento" (arts. 5–33). É a abertura natural da lei e provavelmente onde o estudante começa, mas é lista de requisitos e formas — pouco conflito, e vira quiz sem alma.*

---

## As três funcionalidades centrais

Mais do que três, o MVP não aguenta.

| # | Funcionalidade | Por que é essencial |
|---|---|---|
| **1** | **Combate por questão** | É o pilar (D2). Sem isso não existe produto. |
| **2** | **Agendador de revisão + Catacumbas** | É o diferencial pedagógico (D5). Sem isso é quiz com sprites. |
| **3** | **Progressão persistente** | Sem save e sem sensação de avanço, ninguém volta no dia 2 — e o produto vive de voltar. |

Tudo o mais é consequência dessas três ou fica de fora.

---

## Números do MVP

Números, não adjetivos.

| Item | Quantidade | Observação |
|---|---|---|
| Matéria | **1** | Regime Disciplinar, Lei 8.112/90 arts. 116–142 |
| Questões curadas | **40** | não 150 — ver §Curadoria abaixo |
| Área jogável | **1** | uma repartição/edifício com ~6 salas |
| Tipos de inimigo | **4** | um por bloco temático (deveres, proibições, penalidades, PAD) |
| Chefes | **1** | exige recall guiado, não múltipla escolha |
| Catacumbas de revisão | **1 gerada** | conteúdo vem do agendador, não é mapa fixo |
| Duração de uma sessão | **8–15 min** | a sessão precisa caber num intervalo de estudo real |
| Campanha completa | **~40 min** | primeira passada, sem revisões |

**40 questões, não 150.** A ficha falava em 100–150 — esse é o número do **produto**, não da fatia vertical. Com 4 blocos temáticos e 10 questões por bloco, dá para exercitar o loop, alimentar o agendador e provar retenção. Se 40 provarem que o ciclo funciona, escalar para 150 é trabalho braçal com risco conhecido. Se 40 não provarem, ter 150 não salvaria nada.

---

## Fora do MVP — e por quê

| Fora | Motivo |
|---|---|
| **Painel de retenção** | O mais fácil de adiar e o mais fácil de justificar mantendo. **O dado é coletado desde o dia 1** — só a visualização espera. Isso não contradiz a D1: a plataforma web foi escolhida para *poder* medir, e medir começa agora. |
| **Contas, login, nuvem** | MVP salva local (IndexedDB). Sem conta = sem dado pessoal = sem LGPD no caminho crítico. Enorme economia de escopo e de risco. |
| **Notificações push** | Depende de servidor (Parte A.5) **e** esbarra no Decreto 12.880 art. 9º (Parte C.4). Decisão consciente de adiar até haver conta e parecer. |
| **Streak** | Mesmo motivo — *"recompensas pelo tempo de uso"* é literalmente o inciso III proibido. Adiar até entender o alcance da norma. |
| **Segunda matéria** | Provar o ciclo com uma. Escalar depois. |
| **Vestibular** | D3 + regime simplificado da ANPD (Parte C.4). Menor no produto derruba o regime de agente de pequeno porte. |
| **Multiplayer, ranking, social** | Não serve ao pilar. |
| **App nativo / loja** | Fase 1 e 2 do plano de entrega (Parte A.5), não do MVP. |
| **Monetização** | Não se cobra por algo que ainda não provou reter. |
| **Áudio original** | Efeitos sonoros mínimos; trilha depois. |
| **História ramificada** | Narrativa linear e curta. Ramificação é sonho. |

---

## Sistemas: essencial · desejável · sonho

**Essencial (dentro do MVP):**
- Combate por questão com cronômetro e embaralhamento de alternativas
- Dano bidirecional (errar machuca o jogador)
- Feedback pós-resposta com o artigo de lei que fundamenta
- Agendador de revisão (binário → grade 1/3, SM-2 ou FSRS)
- Catacumba de revisão gerada a partir dos itens vencidos
- Decaimento visível de poder por item vencido
- Persistência local
- Movimento e exploração em mapa de tiles
- Progressão de personagem amarrada ao domínio das matérias

**Desejável (v1.1, depois de provar o ciclo):**
- Painel de retenção
- Contas e sincronização
- Segunda e terceira matérias
- Recall guiado também em inimigos comuns
- Itens, equipamento e cosméticos
- Trilha sonora

**Sonho (talvez nunca):**
- Edital completo com múltiplas leis
- Geração de rota de estudo a partir do edital do usuário
- Simulado cronometrado no formato da banca
- Modo comparativo entre bancas
- Multiplayer / guildas de estudo

---

## Curadoria — o gargalo, tratado como cronograma

O R-03 diz que a curadoria é o projeto de verdade. Então ela tem número e teste.

**Antes do boot, cronometrar a curadoria de 10 questões reais**, ponta a ponta:
1. Localizar no PDF da prova (API pública do Cebraspe — Parte C.2)
2. Conferir contra o gabarito oficial
3. Classificar por bloco temático e dificuldade
4. Amarrar ao artigo de lei correspondente
5. Escrever o feedback pós-resposta
6. Associar ao inimigo/situação

**Extrapolar e decidir:**

| Tempo por questão | 40 questões | Veredito |
|---|---|---|
| ≤ 5 min | ~3 h | tranquilo, pode considerar 60 |
| 10 min | ~7 h | aceitável, mantém 40 |
| 20 min | ~13 h | **reduzir para 24 questões** (6 por bloco) |
| > 30 min | 20 h+ | repensar o formato do feedback — está caro demais |

**Regra:** se o número tiver que cair, cai o **número**, nunca a qualidade. Zero questão inventada (D3) é absoluto.

---

## Critérios de pronto do MVP

Verificáveis, não subjetivos. O R-10 exige medir **as duas coisas** — jogo e estudo. Um sem o outro não é sucesso.

### Funcionais
- [ ] Jogador entra na área, enfrenta os 4 tipos de inimigo, derrota o chefe e completa a campanha
- [ ] Errar uma questão causa dano visível e mostra o artigo de lei que fundamenta a resposta
- [ ] O cronômetro por questão funciona e a ordem das alternativas muda a cada tentativa
- [ ] O chefe exige recall guiado, não múltipla escolha
- [ ] Ao fechar e reabrir, o progresso está intacto
- [ ] Itens vencidos geram uma Catacumba com o conteúdo correto
- [ ] Item vencido **não revisado** reduz o poder do personagem de forma perceptível na tela
- [ ] Recombate fora da Catacumba **não altera** o estado do agendador
- [ ] Roda em Chrome Android e Safari iOS, em retrato, sem borrão e sem barra de rolagem horizontal
- [ ] Nenhuma questão inventada: as 40 rastreáveis a prova, banca e ano

### De produto — os que realmente importam
- [ ] **O CEO jogou 5 dias seguidos por vontade própria**, sem se forçar a testar
- [ ] **Na revisão de 10 dias, a taxa de acerto do conteúdo revisado é maior que a do não revisado** — medida, não impressão
- [ ] Uma pessoa que não é o CEO joga 15 minutos sem precisar de explicação

O penúltimo é o critério que separa este projeto de um quiz com sprites. Se ele falhar, o R-02 se confirmou e a D5 precisa de revisão — não o código.

---

## O que este escopo assume e pode estar errado

- Que **40 questões bastam** para o agendador produzir sinal de retenção em 10 dias. Se a amostra for pequena demais para distinguir, o critério de produto fica inconclusivo — e aí sobe para 60.
- Que **Regime Disciplinar** é recorte suficientemente rico. Se na prática for repetitivo, trocar por um recorte misto entre dois títulos.
- Que **sem streak e sem notificação** ainda dá para medir retorno. Provavelmente o retorno cai — mas medir retorno *sem* muleta de engajamento é um teste mais honesto do pilar.
