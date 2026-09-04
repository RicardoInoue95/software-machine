---
slug: edital-quest
tipo: jogo
status: ativa
criado: 2026-08-27
atualizado: 2026-08-27
---

# GDD — Edital Quest

> Documento candidato à **1ª assinatura do CEO** (Fase 3). **Completo — nenhuma seção pendente.**
> Baseado nas decisões de portão D1–D6 (`00-ficha.md`), na pesquisa (`01-pesquisa.md`) e no escopo (`02-escopo.md`).

---

## 1. Identidade — ✅ confirmada pelo CEO em 2026-08-27

### 1.1 A fantasia

> **Você é um servidor recém-empossado numa repartição onde os vícios do serviço público ganharam forma física — e o único instrumento que os afeta é conhecer a lei.**

Não é "estudar para vencer o monstro". É: **saber a lei *é* a arma**. Onde o colega desinformado é impotente, você age. A fantasia é de **competência num ambiente que pune a incompetência** — exatamente a fantasia que o concurseiro já persegue na vida real, devolvida a ele em forma jogável.

**Por que esta e não outra:** a Lei 8.112/90, no Regime Disciplinar, já é um bestiário. *Inassiduidade habitual*, *improbidade administrativa*, *advocacia administrativa*, *valimento do cargo* — são infrações **nomeadas na própria lei**, prontas para virar inimigos sem que se force metáfora nenhuma. O chefe da fatia vertical é o **Processo Administrativo Disciplinar**: derrotá-lo exige conhecer o rito, não só as penas.

Isso respeita o jogador. O jogo não zomba do concurseiro nem da burocracia — trata o domínio da norma como poder real, que é o que ele é.

### 1.2 A emoção dominante: **maestria**

Uma só rege as outras, e a recomendação é **maestria** — a satisfação de dominar algo difícil e sentir isso acumular.

**Por que não as outras:**
- **Tensão** — o público já vive com excesso dela. Adicionar ansiedade a quem está ansioso é como se perde usuário no dia 4. A tensão entra em dose controlada (cronômetro, chefe), nunca como emoção reitora.
- **Humor** — funciona como **pele**, não como esqueleto. A burocracia brasileira é comédia pronta e o jogo deve usar isso no texto e nos nomes. Mas piada envelhece: no dia 12, o que faz voltar é sentir que se sabe mais, não rir de novo da mesma placa de "volte amanhã".
- **Conforto** — bom para app de hábito, fraco para RPG. Sem atrito não há sensação de conquista.

**Formulação operacional:** humor no tom e na escrita; maestria na estrutura e nas recompensas.

### 1.3 Referências — o que pegar e o que **não** pegar

| Referência | Pegar | **Não** pegar |
|---|---|---|
| **Pokémon (FireRed / Emerald)** | O ciclo limpo encontro → combate por turnos → crescimento; UI legível e sem ornamento; mundo compacto e explorável; a leitura instantânea de "estou ficando mais forte" | **Encontros aleatórios** — cada combate aqui custa esforço mental real, e encontro não desejado vira punição. Combate é sempre visível e opcional. Também não pegar a duração de 20+ horas nem a complexidade de tabela de tipos |
| **Mother 3 / EarthBound** | O cotidiano burocrático transformado em estranho e engraçado; a voz do texto; a disposição de ser absurdo sobre instituições | O ritmo experimental e a estrutura longa. E **não** pegar a devastação emocional — este jogo é acompanhado de véspera de prova, não é lugar para desespero |

*Alternativa considerada: Fire Emblem, pelo peso tático de cada decisão. Descartada — grade tática alonga demais o combate, e a sessão precisa caber em 8–15 minutos.*

### 1.4 Pilar inegociável — confirmação da D2

> **Aprender é jogar. A questão é o combate.**

Qualquer sistema que reintroduza a separação *estudar para depois ser recompensado com jogo* é violação de pilar e deve ser recusado, por mais divertido que seja isoladamente.

### 1.5 Regra de encontro — contribuição do CEO na confirmação

> **O combate é sempre intencional, e inevitável apenas onde o edital exige.**

Formulação do CEO ao confirmar o corte de encontros aleatórios, e que vira **regra de design de posicionamento**:

- **Intencional** — o jogador sempre vê o inimigo e escolhe engajar. Nada de emboscada. Combate custa esforço mental real; combate não desejado é punição, e punição afasta.
- **Inevitável apenas onde o edital exige** — combate obrigatório é reservado ao conteúdo que a prova realmente cobra com peso. O que é periférico no edital é encontro opcional, recompensado mas evitável.

Isso dá ao mapa um critério objetivo de posicionamento: **a incidência do assunto em prova decide se o inimigo bloqueia o corredor ou fica na sala lateral.** O edital vira nível de design, não só fonte de conteúdo.

---

## 2. Core loop

### 2.1 Loop de 30 segundos

```
inimigo ataca com uma questão real
        ↓
cronômetro corre · alternativas embaralhadas a cada tentativa
        ↓
   ACERTOU                        ERROU
        ↓                            ↓
 dano no inimigo            você toma dano
 você não é atingido        aparece o artigo de lei que fundamenta
        ↓                            ↓
        └────────→ repete até alguém cair ←────────┘
```

O feedback do erro **não é punição seca**: mostra o dispositivo legal que resolve a questão. O dano já é a punição; o texto é a razão de ter perdido.

### 2.2 Combate em dois níveis — resposta ao problema do chute

A pesquisa (B.3) mediu o problema: múltipla escolha embute **25% de falso-positivo**; certo/errado da CESPE, **50%**. Um sinal binário contaminado por sorte não sustenta a D4 ("cola vira autossabotagem"), porque o sistema não distingue domínio de chute.

O Quizlet resolveu isso com desvanecimento de andaime, e aqui isso mapeia direto na curva de dificuldade do RPG:

| Inimigo | Formato | Papel |
|---|---|---|
| **Comum** | múltipla escolha / certo-errado | andaime — rápido, mantém o ritmo do combate |
| **Chefe** | **recall guiado** — identificar o artigo, completar o termo operativo | graduação — só domínio passa, chute não |

Um item só é considerado **dominado** depois de passar por recall guiado ao menos uma vez. Múltipla escolha sozinha nunca gradua.

### 2.3 Loop de 30 minutos

```
entrar na área → 3 a 5 combates → subir de nível (desbloqueia bloco temático)
      → chefe → área concluída
                    ⇅
        itens vencidos abrem a entrada da Catacumba
```

Subir de nível **não** é acumular pontos: é **desbloquear um bloco temático da lei**. Progressão de personagem e progressão de conteúdo são a mesma coisa — corolário direto do pilar.

---

## 3. Sistemas

### 3.1 🔴 A muralha do agendador — o sistema mais importante do jogo

A pesquisa (B.3) trouxe o achado que mais afeta a D5: o WaniKani mantém a prática extra **fora** do agendador de propósito. Sem essa separação, *cramming* corrompe o modelo — e uma Catacumba opcional de alto valor é um convite a farmar revisão fácil.

**Regra inegociável — três contextos de combate, só um mexe no agendamento:**

| Contexto | Cria item no SRS | **Altera agendamento** |
|---|---|---|
| Campanha, matéria nova | sim (primeira exposição) | sim |
| **Catacumba (agendada)** | não | **sim** |
| Recombate livre / treino | não | **não** |

Sem isso, a D5 se autodestrói: o jogador farma o que já sabe, o agendador acha que ele domina tudo, e a revisão para de acontecer justamente no conteúdo difícil.

### 3.2 Decaimento visível — mitigação do R-02

O risco número um do produto: **revisão opcional pode simplesmente não acontecer**. Recompensa positiva satura — depois de ter os cosméticos, some o motivo de voltar.

**Mecanismo:** o poder do personagem é **derivado do estado do SRS**. Item vencido e não revisado reduz visivelmente o atributo correspondente. Não é punição arbitrária: é a curva de esquecimento tornada legível na tela.

Efeito no design: revisar deixa de ser bônus e vira **manutenção de poder**. Preserva a elegância da D5 — nada bloqueia o mapa principal — mas remove a dependência exclusiva de recompensa positiva.

### 3.3 Agendador

Decisão técnica vinda da pesquisa (B.1/B.2): **mapear binário em dois pontos de uma escala de quatro** — `errou → grade 1`, `acertou → grade 3`, nunca 2 ou 4. É o que Anki e RemNote fazem em cartão de digitar resposta, e permite usar **SM-2 ou FSRS de prateleira** sem inventar modelo.

*Half-Life Regression do Duolingo foi **descartado**: o `p` dele é fração `acertos/vistas`, e a fórmula degenera em sinal binário. O próprio Duolingo abandonou em 2020.*

Estado por par (usuário, questão): `easiness` (2,5 inicial, piso 1,3), `interval`, `repetitions`, `due_date`, `lapses`.

### 3.4 Classificação

**Essencial (MVP):** combate por questão · dano bidirecional · feedback com artigo de lei · cronômetro e embaralhamento · recall guiado no chefe · agendador · Catacumba gerada · muralha do agendador · decaimento visível · persistência local · mapa e exploração · progressão por bloco temático

**Desejável (v1.1):** painel de retenção · contas e sincronização · segunda matéria · recall guiado em inimigo comum · itens e equipamento · trilha sonora

**Sonho:** edital completo · rota de estudo gerada do edital do usuário · simulado no formato da banca · modo comparativo entre bancas · guildas de estudo

---

## 4. Arte, áudio e pipeline

### 4.1 Duas zonas visuais — decisão obrigatória

A pesquisa (A.4) mostrou que estética e produto colidem aqui, e que a colisão tem lado certo:

| Zona | Conteúdo | Como |
|---|---|---|
| **Mundo** (canvas) | mapa, sprites, animação de combate, diálogo curto, HUD, números de dano | pixel art fiel a 240×160, `BitmapText` |
| **Leitura** (DOM sobre o canvas) | **enunciado, alternativas, artigo de lei, feedback** | resolução do dispositivo, tipografia legível, estilizada para combinar |

**O argumento decisivo:** a WCAG 2.2 isenta *jogos* da regra de reflow, mas a isenção cobre o canvas — **não** a interface de leitura. E o critério 1.4.4 exige texto escalável a 200% sem perda; texto rasterizado em canvas é funcionalmente imagem de texto e **não consegue satisfazê-lo**. Para um produto de estudo, isso não é detalhe de acessibilidade — é o núcleo do uso.

**Nunca renderizar parágrafo de lei a 8px.** A tela do GBA tinha 2,9 polegadas a 30cm do rosto; um celular a distância de braço é outra situação de leitura. Fidelidade é *visual*, não meta de legibilidade.

### 4.2 Fonte

**Pixeloid** (SIL OFL 1.1) — 1.141 glifos, latim acentuado completo. É a única do levantamento com cobertura acentuada documentada.

⚠️ **Os charsets nativos de `RetroFont` do Phaser não têm um único caractere acentuado.** Autorar charset customizado com todos os acentos, **maiúsculos e minúsculos, antes de gerar qualquer atlas** — adicionar glifo depois obriga a regerar tudo. Gerar o XML com `msdf-bmfont-xml`. *(`load.bitmapFont()` aceita apenas XML, não JSON.)*

### 4.3 Direção de arte

Paleta limitada de GBA; repartição pública como cenário (balcões, arquivos, corredores, cadeiras de plástico). Inimigos são **infrações personificadas**, com nome retirado da própria lei. O humor mora na arte e nos nomes; a dignidade, no tratamento do jogador.

Filtros nativos do Phaser 4 `Quantize` + `Barrel` dão o aspecto de LCD de GBA no nível da câmera, de graça.

### 4.4 Áudio

MVP: efeitos mínimos (acerto, erro, dano, vitória). Trilha é desejável, não essencial. **Quem produz arte e áudio é pergunta aberta para o CEO** — e é risco de cronograma, não detalhe.

---

## 5. Plataforma e stack

Decorre da **D1** (estética GBA, web/mobile) e da pesquisa Parte A.

| Camada | Escolha | Por quê |
|---|---|---|
| Engine | **Phaser 4.2.x** | 269k downloads/semana, Tiled nativo, template React oficial, `rexDialogQuest` já pronto para sequência de múltipla escolha |
| Casca | **React 19 + Vite 6** | template oficial `phaserjs/template-react-ts`; Phaser em **uma** rota, carregado sob demanda |
| Ponte | `EventBus` (singleton `Phaser.Events.EventEmitter`) | padrão do template oficial |
| Persistência | **IndexedDB** | sem conta no MVP = sem dado pessoal = sem LGPD no caminho crítico |
| Agendador | **SM-2** (FSRS depois) | binário → grade 1/3; SM-2 é mais simples para começar |
| Mapa | **Tiled** → JSON | integração nativa do Phaser |
| Fonte | **Pixeloid** + `msdf-bmfont-xml` | acentos |
| Entrega | **PWA** | lojas são Fase 1 e 2, fora do MVP |

### Alternativas descartadas

- **GBA real (devkitARM/Butano)** — D1. Conteúdo congelado e retenção não mensurável matam a proposta de edtech.
- **Excalibur 0.32** — tecnicamente mais adequado (145 KB, TypeScript-first, `ex.Resolution.GameBoyAdvance` embutido, cobertura de Tiled superior). Descartado por risco: 7k downloads/semana contra 269k, ainda 0.x com breaking changes declaradas, plugin de Tiled parado desde dez/2024. **Não vale essa troca aprendendo a stack.**
- **Kaplay** — voltado a game jam, dividido em duas linhas incompatíveis.
- **PixiJS puro** — renderizador, não framework. Seria construir o framework à mão.
- **Half-Life Regression** — degenera em sinal binário (§3.3).
- **Tauri** — documentação própria admite que a experiência mobile não está pronta.

### Armadilhas registradas para o boot

- `roundPixels` passou a **`false`** por padrão no Phaser 4 — mudança silenciosa que quebra pixel art
- Escala inteira calculada em **pixels de dispositivo**, não CSS — em CSS perde-se dois terços da tela de um celular moderno. `MAX_ZOOM` do Phaser é armadilha: arredonda em CSS e nunca recalcula
- `autoRound: false`, `expandParent: false`, `resizeInterval: 100`
- `100svh`, **nunca** `100dvh`
- StrictMode desmonta e remonta: guardar com ref e limpar com `game.destroy(true)` **mais** `ref = null`
- Nunca `setState` por frame
- `viewport-fit=cover` obrigatório, senão `env(safe-area-inset-*)` resolve para `0px`
- Retrato como padrão — `ScreenOrientation.lock()` não funciona no iOS

---

## 6. Equipe de agentes — esboço preliminar

> O organograma formal (áreas de atuação, gatilhos de bloqueio, interfaces) é a **Fase 4 — Blueprint**. Aqui é só dimensionamento.

Projeto é candidato a **blueprint em regime provisório** (R-07): stack nova para o CEO, com árvore que só o código revela. Equipe inicial enxuta.

| Agente | Recorte | Origem |
|---|---|---|
| `game-designer` | GDD, balanceamento, narrativa e **curadoria das questões** — aqui conteúdo é design | arquétipo existente, adaptado |
| `engine-dev` | Phaser: mapa, combate, render, e o módulo do agendador | arquétipo existente |
| `frontend-dev` | React: camada de leitura em DOM, acessibilidade, persistência | arquétipo existente |
| `qa` | testes e, principalmente, **verificação dos critérios de produto** | arquétipo existente |

**Quem não existe e por quê:** sem `platform-specialist` (a plataforma é o navegador, não hardware restrito); sem `backend-dev` nem `devops` (o MVP não tem servidor); sem `security-reviewer` (não há dado pessoal enquanto não houver conta — **entra obrigatoriamente na v1.1**); sem `asset-pipeline` (volume de assets do MVP não justifica agente dedicado).

---

## 7. MVP — fatia vertical

Detalhado em `02-escopo.md`. Resumo:

**Uma frase:** um jogador estuda **Regime Disciplinar da Lei 8.112/90** derrotando inimigos com questões reais, e volta dias depois porque a revisão agendada o deixa mais forte.

**Números:** 1 matéria · **40 questões** curadas · 1 área com ~6 salas · 4 tipos de inimigo · 1 chefe · sessão de 8–15 min · campanha de ~40 min.

**Fonte das questões — emenda D3-a:** exclusivamente **Cebraspe**, via a API pública (`apis.cebraspe.org.br`). O Cebraspe imprime permissão expressa de reprodução para fins didáticos mediante citação da fonte; a FGV, ao contrário, reserva todos os direitos e proíbe acesso automatizado. Cada questão carrega procedência rastreável — **banca, concurso, ano, cargo** — e a citação da fonte é **requisito da permissão**, não cortesia. FGV fica para depois do MVP.

**Fora:** painel de retenção (mas **o dado é coletado desde o dia 1**) · contas · push · **streak** · segunda matéria · vestibular · lojas · monetização.

**Critérios de pronto — os dois que decidem:**
- [ ] O CEO jogou **5 dias seguidos por vontade própria**
- [ ] Na revisão de 10 dias, **acerto do conteúdo revisado > não revisado**, medido

Se o segundo falhar, o R-02 se confirmou e a **D5 precisa de revisão — não o código**.

---

## 8. Roadmap pós-MVP

Curto e sem compromisso. **Nada aqui é promessa.**

1. Painel de retenção (o dado já existe)
2. Contas e sincronização — entra `security-reviewer`, entra LGPD
3. Segunda e terceira matérias da 8.112
4. TWA na Google Play (+US$ 25) · **começar cedo o teste fechado de 12 testadores por 14 dias**
5. Capacitor no iOS (+US$ 99/ano) quando a retenção justificar — resolve notificação local sem servidor
6. Streak e notificações **somente após parecer sobre o Decreto 12.880, art. 9º**

---

## Parecer do Diretor Geral

**Recomendo com ressalvas.**

O núcleo é forte e as cinco decisões de portão foram acertadas — mataram seis riscos sérios antes da primeira linha de código. A pesquisa confirmou a viabilidade técnica e melhorou substancialmente o quadro jurídico (o Cebraspe imprime permissão expressa para fins didáticos e oferece API pública sem autenticação).

**Três ressalvas, e nenhuma impede a assinatura:**

**1. R-01 — a incerteza jurídica é sua para assumir.** Há uma única decisão diretamente relacionada (TJSP, Apelação 1112376-68.2021.8.26.0100), **por maioria**, sobre **certificação privada**, não concurso público, e em câmara estadual — não no STJ. Os incumbentes operam há 12–18 anos sem litígio conhecido, e o Qconcursos foi adquirido por R$ 208 milhões em 2021, transação que envolveria diligência sobre exatamente isto. Isso é sinal, não garantia. **Seguir sem advogado é decisão legítima do CEO — mas precisa ser decisão, registrada, não omissão.**

**2. R-02 — a D5 é aposta não provada.** Revisão opcional é elegante e contraria como repetição espaçada funciona. Propus duas mitigações no GDD (muralha do agendador §3.1, decaimento visível §3.2), mas ambas são hipóteses até a fatia vertical medir. O critério de pronto foi escrito para expor isso em 10 dias, não em 6 meses.

**3. R-03 — cronometrar a curadoria antes do boot.** Dez questões, ponta a ponta, e extrapolar. Se sair a mais de 20 minutos por questão, o MVP cai para 24 questões. **O número cede; a qualidade, nunca.**

**Uma observação que não é ressalva:** o ECA Digital (Lei 15.211/2025, em vigor desde março) proíbe literalmente *"recompensas pelo tempo de uso"* e *"notificações excessivas"*. Streak e push — dois pilares clássicos de gamificação de estudo — estão fora do MVP por isso, e a ANPD multou o TikTok em R$ 153,7 milhões em agosto exatamente nessa família de infração. Ficar em **concurso, adultos** também preserva o regime simplificado da ANPD para desenvolvedor solo. Essa restrição, que parecia perda, empurrou o escopo para o lugar certo.

**O documento está completo.** Nenhuma seção pendente.

### ⚠️ O que a assinatura significa

Assinar este GDD não é só aprovar o design. Carrega junto **três aceitações explícitas**, e é por isso que estão listadas aqui e não escondidas no corpo do texto:

1. **Aceitação do R-01** — seguir com questões reais de banca **sem parecer jurídico prévio**, com base no que a pesquisa levantou (permissão expressa do Cebraspe para fins didáticos + 12–18 anos de operação dos incumbentes sem litígio conhecido). É decisão de risco do CEO, registrada, não omissão. *Se você preferir consultar advogado antes, não assine ainda — diga, e eu paro o projeto na Fase 2 até haver parecer.*
2. **Aceitação do R-02** — a D5 (revisão opcional) é aposta não provada. As mitigações §3.1 e §3.2 são hipóteses até a fatia vertical medir.
3. **Compromisso com o teste de curadoria (R-03)** — cronometrar 10 questões ponta a ponta **antes do boot**, e reduzir o número se passar de 20 min por questão.

---

*Aprovado pelo CEO em: **2026-08-27**, com a emenda D3-a (apenas Cebraspe no MVP)*
*Parecer do Diretor Geral: recomenda com ressalvas — R-01, R-02 e R-03 aceitos pelo CEO na assinatura*
*Blueprint assinado em: **2026-08-27***
*Boot realizado em: **2026-08-27** - projetos-ativos/edital-quest/*
