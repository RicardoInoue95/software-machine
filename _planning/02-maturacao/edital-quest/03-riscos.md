---
slug: edital-quest
tipo: jogo
status: maturando
criado: 2026-08-25
atualizado: 2026-08-25
---

# Riscos — Edital Quest

Ordenados por **quanto podem matar o projeto**, não por probabilidade. Todo risco tem mitigação concreta ou aceitação explícita do CEO — risco sem uma das duas coisas é risco não tratado.

Legenda de severidade: 🔴 mata o projeto · 🟠 custa meses · 🟡 custa semanas

---

## R-01 🔴 Origem legal das questões

**O risco:** o MVP depende de 100–150 questões reais de CESPE/FGV. Se reproduzi-las exigir licença, o produto não pode existir na forma pensada. Quatro coisas distintas com estatutos possivelmente diferentes: o **enunciado**, o **gabarito oficial**, o **comentário/classificação de terceiros**, e o **nome da banca** usado como rótulo.

**Por que é o primeiro da lista:** é o único risco capaz de invalidar o projeto inteiro *antes* da primeira linha de código — e o único que não se resolve com esforço de engenharia.

**Mitigação:** pesquisa factual em andamento (`01-pesquisa.md`) sobre Lei 9.610/98 art. 8º, prática das empresas estabelecidas do setor e fontes abertas. **Isto não substitui advogado.** A decisão de seguir sem parecer jurídico, se for o caso, é do CEO e fica registrada aqui.

**Estado:** ⏳ aguardando pesquisa.

---

## R-02 🔴 Revisão opcional pode simplesmente não acontecer

**O risco:** a Decisão D5 (Dungeons de Reciclagem) é elegante em design de jogo e **arriscada em pedagogia**. Revisão espaçada funciona porque é agendada pelo algoritmo, não pela vontade do aluno. Ao tornar a revisão opcional, o projeto aposta que a recompensa do jogo é forte o bastante para substituir a obrigação.

**Se a aposta falhar:** o aluno avança na campanha, nunca revisa, esquece o conteúdo, e o produto vira um quiz com sprites — exatamente o que a D2 quis evitar. E o pior: **isso só aparece em 30 dias**, quando a curva de esquecimento cobra.

**Modos de falha específicos a vigiar:**
- Aluno *farma* revisão fácil e evita o conteúdo difícil — que é justamente o que precisa revisar
- Dívida de revisão acumula até virar intimidante demais para começar
- Recompensa cosmética satura: depois de ter os itens, some o motivo de voltar

**Mitigação candidata (a validar na fatia vertical):** decaimento visível — conteúdo não revisado *enfraquece* o personagem de forma perceptível, transformando revisão em manutenção de poder em vez de bônus. Isso preserva a elegância da D5 sem depender só de recompensa positiva.

**Estado:** ⏳ pesquisa sobre SRS gamificado em andamento. Este é o **risco de produto número um**.

---

## R-03 🔴 A curadoria de conteúdo é o projeto de verdade

**O risco:** o CEO decidiu curadoria manual estrita (D3). Correto para credibilidade — e caro. Cada questão precisa ser: extraída, verificada contra o gabarito, classificada por assunto e dificuldade, e amarrada a um inimigo/situação na narrativa. Isso é trabalho humano que não escala com código.

**Consequência de subestimar:** o jogo fica pronto e vazio. Ou pior — o CEO corta a curadoria pela metade sob pressão de prazo e o produto perde a única coisa que o diferencia.

**Mitigação:** tratar a curadoria como **entregável de cronograma com número**, não como tarefa de fundo. Antes do boot, cronometrar a curadoria de **10 questões reais** e extrapolar. Se 150 questões custarem mais que o jogo inteiro, o MVP reduz o número — não a qualidade.

**Aceitação necessária do CEO:** confirmar que 100–150 é piso de credibilidade e não número escolhido por conforto.

---

## R-04 🟠 Escopo total é grande demais para uma pessoa

**O risco:** o MVP como está descrito contém **quatro produtos**: um RPG (mapas, combate, progressão, save), um motor de repetição espaçada, um banco de questões curado, e um painel de retenção. Cada um é um projeto respeitável sozinho.

**Mitigação:** a fatia vertical precisa ser brutal. Uma proposta a discutir em `02-escopo.md`: **um assunto da Lei 8.112/90, um mapa, um chefe, ~30 questões, SRS funcionando, sem painel.** O painel de retenção é a coisa mais fácil de adiar e a mais fácil de justificar mantendo — vigiar essa tentação.

**Nota:** a D1 justificou a plataforma web *pela* medição de retenção. Cortar o painel do MVP não contradiz a D1 desde que o **dado seja coletado** desde o início — mostrar é que pode esperar.

---

## R-05 🟠 Legibilidade × fidelidade estética

**O risco:** GBA autêntico é 240×160 com fonte bitmap minúscula. Este jogo mostra **artigos de lei e enunciados de concurso** — parágrafos, não caixas de diálogo de 3 linhas. Fidelidade estética levada ao pé da letra produz um app que cansa a vista em 20 minutos, num público que estuda por horas.

**Mitigação:** a D1 já libera a saída — 240×160 virou escolha estética, não imposição de hardware. Definir no GDD **duas zonas visuais**: mundo do jogo em pixel art fiel, e camada de leitura (enunciado, alternativas, texto de lei) em tipografia legível que *combine* com a estética sem imitá-la. Precedente comum em jogos pixel-art modernos.

**Estado:** ⏳ pesquisa sobre tipografia em jogos pixel art em andamento.

---

## R-06 🟠 Concorrência estabelecida e enorme

**O risco:** Qconcursos, TEC, Gran Cursos e Estratégia têm bancos de centenas de milhares de questões, marca, e anos de SEO. Um MVP com 150 questões não compete em conteúdo.

**Enquadramento:** este projeto não deve competir em **volume** — ele compete em **engajamento e retenção**. O concorrente real não é o Qconcursos; é a **desistência**. O aluno que abandona a planilha no dia 12.

**Mitigação:** o GDD precisa declarar isso explicitamente, e a métrica de sucesso do MVP precisa refletir engajamento (dias consecutivos, taxa de retorno, retenção medida) e não cobertura de edital.

**Estado:** ⏳ aguardando pesquisa de mercado.

---

## R-07 🟡 Stack nova para o CEO

**O risco:** Phaser + React + persistência + SRS é bastante coisa para aprender enquanto se constrói. O blueprint corre o risco de estar errado em pontos que só o código revela.

**Mitigação:** este projeto é candidato claro a **blueprint em regime provisório**, com revisão obrigatória no marco *"primeira questão funcionando como combate"*. Equipe inicial menor (3 agentes), especializando depois que se souber onde está o trabalho de verdade.

---

## R-08 🟡 LGPD — dados de desempenho de estudo

**O risco:** o produto armazena identidade, respostas, acertos, tempos e agenda de estudo. Isso é dado pessoal. Se houver usuários menores de 18 (vestibulandos), o regime aperta — mas o MVP é concurso, o que reduz a exposição.

**Mitigação:** decidir base legal, política de privacidade, fluxo de consentimento e mecanismo de exclusão **antes** do primeiro usuário real — não antes do primeiro código. Agente `security-reviewer` obrigatório na equipe.

**Estado:** ⏳ aguardando pesquisa.

---

## R-09 🟡 Motivação — a armadilha do meta-projeto

**O risco:** projeto longo, com fase de infraestrutura antes de qualquer coisa divertida aparecer na tela. Histórico recente da fábrica mostra tendência a construir estrutura em vez de produto — a fundação da Fábrica, os ajustes e a ideia do painel vieram todos antes do jogo.

**Ironia útil:** o tema do produto é *não procrastinar*. A fatia vertical deve ser usada como prova de que o ciclo funciona — **no próprio CEO, primeiro**. Se o autor não usa, ninguém usa.

**Mitigação:** fatia vertical curta e jogável cedo. Nada de "primeiro a arquitetura, depois o jogo".

**Aceitação:** risco assumido conscientemente pelo CEO.

---

## R-10 🟡 O equilíbrio jogo × estudo

**O risco:** dois fracassos simétricos. Jogo bom demais e estudo raso → o aluno se diverte e não passa no concurso. Estudo rigoroso demais e jogo fraco → volta a ser planilha, e a nostalgia não segura ninguém.

**Mitigação:** critério de pronto do MVP precisa medir **as duas coisas**: alguém jogou por vontade própria **e** acertou mais na revisão de 10 dias do que acertaria sem o jogo. Um sem o outro não é sucesso.

---

## Riscos descartados por decisão do CEO

| Risco | Como foi eliminado |
|---|---|
| Conteúdo congelado na ROM, sem atualizar edital | D1 — plataforma web |
| Impossibilidade de medir retenção | D1 — plataforma web |
| Credibilidade destruída por questão inventada | D3 — zero questões de IA |
| Monitoramento invasivo violando LGPD | D4 — sem rastreio de tela/abas |
| Tédio de refazer fases já vencidas | D5 — dungeons opcionais de alto valor |
| Estudo como castigo, jogo como suborno | D2 — a questão **é** o combate |

Seis riscos sérios eliminados por decisão de design antes da primeira linha de código. É exatamente para isso que a maturação existe.
