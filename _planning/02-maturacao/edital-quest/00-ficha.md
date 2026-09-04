---
slug: edital-quest
tipo: jogo
status: maturando
criado: 2026-08-25
atualizado: 2026-08-25
---

# Edital Quest — RPG Educacional Retrô

## O estalo (1 frase)
Um RPG com estética de Game Boy Advance onde o *grind* não é matar monstro aleatório — é **dominar o conteúdo real de editais de concurso público**, com questões de bancas reais traduzidas para a narrativa.

## De onde veio
Uma dor concreta e reconhecível: estudar para concurso é exaustivo, solitário e maçante, com alta evasão. O aluno decora lei seca sem contexto e perde para a procrastinação. A hipótese é que a nostalgia retrô mais estrutura de RPG sustentam engajamento onde a planilha de estudo falha.

## Para quem
**Concurseiro** — confirmado por implicação na Decisão 3 (Lei 8.112/90, Técnico Administrativo). Vestibular fica fora do MVP. *Pendente de confirmação explícita do CEO.*

## Por que agora
Público grande, motivado e acostumado a pagar por preparação. Estética GBA é barata de produzir comparada a 3D ou pixel art elaborada.

## Diferencial estratégico
Transformar o sofrimento do edital em jornada de herói em pixel art, unindo nostalgia e retenção técnica.

---

# DECISÕES DE PORTÃO — assinadas pelo CEO em 2026-08-25

As cinco perguntas que travavam a maturação foram respondidas. Estas decisões são **vinculantes** para o GDD: mudá-las exige nova decisão explícita do CEO, registrada aqui.

## D1 — Plataforma: **Estética GBA** (web/mobile, stack moderna)

Nostalgia visual e sonora com infraestrutura web dinâmica. GBA real foi **descartado**.

**Justificativa do CEO:** o objetivo é um produto que as pessoas usem para passar em concursos. Editais mudam toda semana, leis são revogadas, e a retenção precisa ser medida em tempo real. Uma ROM é engenharia fascinante e péssima para edtech escalável.

**Consequências que o GDD herda:**
- Conteúdo atualizável e medição de retenção passam a ser possíveis — e portanto **obrigatórios**, porque eram a justificativa da escolha.
- O CEO **não** vai aprender programação retrô neste projeto. Se isso ainda for desejado, é outro projeto (candidato a spike ou ficha nova).
- A restrição de 240×160 vira **escolha estética**, não imposição de hardware — e escolha estética pode ser flexibilizada onde prejudicar a leitura (ver risco R-04).

## D2 — O RPG **é** a mecânica (a questão é o combate)

Não é Pomodoro com sprites. O conhecimento é o inventário e o poder de ataque; acertar uma pegadinha da CESPE desfere o golpe crítico; errar é tomar dano e ver o monstro avançar.

**Justificativa do CEO:** "Pomodoro com sprites vira um fardo. O estudo não pode ser o castigo para liberar o prêmio de jogar. Aprender é jogar."

**Consequência:** o **pilar inegociável** do projeto. Qualquer sistema que reintroduza a separação estudo→recompensa é violação de pilar e deve ser recusado pelo `game-designer`.

## D3 — Conteúdo: **zero questões geradas por IA**

MVP focado num **micro-edital de entrada**: Lei 8.112/90, cargo Técnico Administrativo. Curadoria **manual e estrita** de 100–150 questões reais de bancas consolidadas (FGV/CESPE), extraídas de bancos públicos abertos. Qualidade acima de quantidade.

**Consequências:**
- Questão inventada por IA e apresentada como real destruiria a credibilidade — proibição absoluta, não preferência.
- Curadoria manual é trabalho real e vira item de cronograma, não nota de rodapé.

### D3-a — Emenda de 2026-08-27: **apenas Cebraspe no MVP**

A pesquisa (Parte C.2) mostrou que Cebraspe e FGV estão em posições opostas:

| Banca | Postura |
|---|---|
| **Cebraspe** | permissão **expressa impressa na prova** — *"É permitida a reprodução deste material apenas para fins didáticos, desde que citada a fonte"*, confirmada em 6 documentos. Sem termos de uso no site. API pública sem autenticação, 425 concursos encerrados |
| **FGV** | termos reservam **todos** os direitos e **proíbem acesso automatizado**. Restrição **contratual** — pode vincular mesmo que o conteúdo em si não seja protegível |

**As 40 questões do MVP vêm exclusivamente do Cebraspe.** Custo próximo de zero (o Cebraspe cobre Regime Disciplinar com sobra) e troca a incerteza do R-01 por permissão escrita. FGV entra depois, com volume que justifique conversa jurídica.

Toda questão carrega procedência rastreável: **banca, concurso, ano, cargo** — e a citação da fonte é requisito da permissão, não cortesia.

## D4 — Anti-distração: **autossabotagem inteligente**, sem monitoramento

Monitoramento de tela ou abas foi **descartado** — fere a LGPD e cria atrito tóxico. O jogo não proíbe a cola; torna a cola inútil a médio prazo.

**Mecanismos:** cronômetro agressivo por questão; alternativas embaralhadas; revisão espaçada que cobra a lacuna 3 dias depois, dentro do combate.

**Consequência:** colar deixa de ser infração e vira **tiro no pé visível ao próprio jogador**. O sistema precisa *mostrar* essa consequência, senão a lição não é aprendida.

## D5 — Revisão: **Dungeons de Reciclagem**

Resolve o conflito entre avanço de RPG e revisão espaçada. Itens vencidos (3, 10, 30 dias, padrão Anki) **não bloqueiam** o mapa principal — manifestam-se como **Catacumbas de Bônus** e **Bosses de Reciclagem**, opcionais e de alto valor.

Vencer uma masmorra de revisão concede os melhores cosméticos, mana ou *buffs* para a campanha principal. O aluno revisa porque isso o fortalece.

**Consequência e risco:** revisão **opcional** é elegante em design e arriscada em pedagogia — se a recompensa não for forte o suficiente, o aluno não revisa e a retenção morre. Ver risco R-03; é o risco de produto número um do projeto.

---

## D6 — Identidade (confirmada em 2026-08-27)

O CEO confirmou as quatro propostas do Diretor sem alteração. Detalhamento em `04-gdd.md` §1.

- **Fantasia:** servidor recém-empossado numa repartição onde os vícios do serviço público ganharam forma física; conhecer a lei é a única arma que os afeta. Inimigos são infrações nomeadas na própria 8.112; o chefe é o PAD.
- **Emoção:** **maestria** como esqueleto, **humor** como pele. *"O concurseiro já carrega toneladas de ansiedade; o jogo precisa desafiar a mente dele, não esmagar o seu emocional."*
- **Referências:** Pokémon FireRed (pegar o ciclo limpo e a UI legível; **não** pegar encontro aleatório) e Mother 3 (pegar o absurdo cotidiano; **não** pegar a devastação emocional).
- **Pilar inegociável:** *"Aprender é jogar; a questão é o combate."*

**Contribuição do CEO que virou regra de design:** *"o combate precisa ser intencional e inevitável apenas onde o edital exige."* A incidência do assunto em prova passa a decidir se o inimigo bloqueia o corredor ou fica na sala lateral — o edital vira nível de design, não só fonte de conteúdo. Registrado no GDD §1.5.

---

## Estado da maturação

| # | Pergunta de portão | Estado |
|---|---|---|
| 1 | Plataforma | **respondida** (D1) |
| 2 | Recompensa × mecânica | **respondida** (D2) |
| 3 | Conteúdo | **respondida** (D3) |
| 4 | Concurso **ou** vestibular | **respondida** — concurso; reforçada pelo regime da ANPD (pesquisa C.4) |
| 5 | Progressão × revisão | **respondida** (D5) |
| 6 | Anti-distração | **respondida** (D4) |
| 7 | Identidade | **respondida** (D6) |

### Critérios de saída da maturação

- [x] MVP cabe em uma frase e tem no máximo 3 funcionalidades centrais
- [x] Stack decidida com justificativa escrita e alternativas descartadas registradas
- [x] Todo risco com mitigação ou aceitação — *R-01 depende da assinatura do CEO*
- [x] Critérios de pronto mensuráveis, cobrindo jogo **e** estudo
- [x] Parecer do Diretor emitido: **recomenda com ressalvas**

**Maturação concluída em 2026-08-27. Aguardando 1ª assinatura do CEO.**
