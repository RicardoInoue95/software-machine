---
slug: edital-quest
tipo: jogo
status: maturando
criado: 2026-08-25
atualizado: 2026-08-25
---

# Pesquisa — Edital Quest

| Parte | Assunto | Estado |
|---|---|---|
| **A** | Stack técnica (engine, arquitetura, renderização, texto, entrega mobile) | ✅ concluída |
| **B** | Repetição espaçada (algoritmo, dados, SRS gamificado) | ⏳ em andamento |
| **C** | Origem das questões e conformidade (direito autoral, LGPD) | ⏳ em andamento |

---

# PARTE A — Stack técnica

*Levantada em 2026-08-25 contra fontes primárias (npm, docs oficiais, caniuse/MDN, WebKit/Chrome dev blogs).*

## A.1 Engine — **Phaser 4.2.x** ✅

| | Phaser 4.2.1 | Excalibur 0.32 | Kaplay | PixiJS 8 |
|---|---|---|---|---|
| Downloads/semana | **269.319** | 7.095 | 7.783 | 1.032.393¹ |
| Gzipped | 356 KB | **145 KB** | 67 KB | 258 KB |
| GitHub | **40.2k ★** | 2.3k ★ | — | — |
| Maturidade | 13+ anos | *"0.x, rough around the edges"* | fork do Kaboom abandonado | renderizador, **não** framework |
| Tiled embutido | ✅ nativo | ✅ plugin (parado desde **dez/2024**) | ASCII apenas | plugin comunitário |

¹ *Inflado por uso fora de jogos (dataviz, UI WebGL). PixiJS não tem cenas, input, áudio, tilemaps, câmeras nem tweens.*

**Decisão: Phaser 4.2.x.** Você precisa de tilemap + cenas + input + áudio + câmera + tween + diálogo **no primeiro dia**, sozinho, aprendendo a stack. É a única opção onde tudo isso é nativo ou está a um `npm install`.

**O contra-argumento honesto:** Excalibur é tecnicamente mais adequado a este briefing — TypeScript-first, menos de metade do peso, cobertura de Tiled superior, e traz `ex.Resolution.GameBoyAdvance` como **preset literal** junto de `pixelArt`/`snapToPixel`, resolvendo quase de graça o problema da seção A.3. Mas: 7k downloads contra 269k, ainda 0.x com breaking changes declaradas, plugin de Tiled parado há 20 meses, e você depuraria sozinho. **Não vale essa troca enquanto se está aprendendo.**

**Achado que importa para a D2:** o pacote `phaser4-rex-plugins` (mantido para v4) traz 46+ componentes de UI, incluindo **`rexDialogQuest`** — construído especificamente para conduzir sequências de múltipla escolha (`quest.start()`, `update-choice`, `update-dialog`, `quest.next()`). **A UI do seu loop de combate já existe pronta.**

Também úteis: `rexUI TextBox` (efeito de digitação com paginação), `rexBBCodeText` (destacar o trecho operativo de um artigo de lei), `rexUI ScrollablePanel` (rolar artigo longo dentro do canvas).

### Armadilhas do Phaser 4 a registrar no GDD

- **`roundPixels` passou a `false` por padrão** (era `true` no v3). Mudança silenciosa que quebra pixel art.
- **O renderizador Canvas está oficialmente depreciado** — usar `type: Phaser.WEBGL`.
- **WebGPU é fundação, não entregue.** Phaser 4 é WebGL2.
- Parser de Tiled **não suporta tilesets do tipo "Collection of Images"** — todo tile precisa estar numa imagem única.
- Filtros nativos `Quantize` + `Barrel` dão um visual de LCD de GBA convincente no nível da câmera, de graça.
- **Phaser AE** é um produto proprietário e pago, sem relação com o Phaser 4 (MIT, gratuito). Não confundir.

## A.2 Arquitetura — **casca React, Phaser em UMA rota** ✅

Existe template oficial e ele já é Phaser 4: **`phaserjs/template-react-ts`** (Phaser 4 + React 19.0.0 + Vite 6.3.1 + TS 5.7.2).

O padrão são dois arquivos: um `EventBus.ts` (singleton `Phaser.Events.EventEmitter`) e um `PhaserGame.tsx` (a ponte). React→Phaser via `EventBus.emit()`, Phaser→React via `EventBus.on()`.

**Divisão recomendada:**

| Camada | O que vive nela |
|---|---|
| **React (páginas normais)** | login, painel, estatísticas, revisão de questões, configurações, cobrança |
| **Phaser (rota `/jogo`, carregado sob demanda)** | mundo, sprites, tilemap, animação de combate, diálogo curto |
| **DOM sobre o canvas** | enunciado da questão, alternativas, painel de leitura do artigo de lei, HUD textual |

**Não** tentar manter instância do Phaser viva entre rotas. Sair de `/jogo` → desmonta → `game.destroy(true)`.

### As sete armadilhas documentadas

1. **StrictMode desmonta e remonta em dev**, destruindo o canvas. Guardar com `if (gameRef.current) return` e limpar com `game.destroy(true); gameRef.current = null` — **sem o `null`, o remount legítimo fica bloqueado; sem o `destroy(true)`, o game loop queima CPU para sempre.** O README oficial não avisa.
2. **Nunca passar estado do React como prop para uma cena.** Cenas não re-renderizam.
3. **Closures obsoletas** — listener do Phaser que captura estado do React vê o valor do mount para sempre. Usar `useRef` ou `setState(prev => …)`.
4. **60 fps vira 60 re-renders/s** se houver `setState` em evento por frame. Emitir só em mudança de estado.
5. Teclas de seta movem o personagem **e** rolam a página — `preventDefault()`.
6. Phaser pesa ~356 KB gzip: `const Phaser = (await import('phaser')).default`.
7. Next.js exige `dynamic(..., { ssr: false })` — Phaser toca `window` no import.

**Sobre `Phaser.GameObjects.DOMElement`** (HTML dentro do espaço do canvas): existe, mas *"não pode ser mesclado à display list"* (sem z-order entre DOM e sprites) e não aceita input pelo Phaser. Serve para um overlay raro; **não é arquitetura.**

## A.3 Renderização pixel-perfect — o achado mais valioso 🔴

**Calcule a escala inteira em pixels de DISPOSITIVO, não em pixels CSS.**

| Aparelho (paisagem) | CSS px | DPR | Escala floor em **CSS** | Em **dispositivo** | Tela usada |
|---|---|---|---|---|---|
| iPhone 15/16 | 852×393 | 3 | 2× | **7×** | 56% → **95%** |
| Pixel 7 | 915×412 | 2.625 | 2× | **6×** | 52% → **89%** |

Arredondar em pixels CSS **joga fora dois terços da tela de um celular moderno**. Arredondar em pixels de dispositivo continua perfeitamente pixel-perfect — cada pixel de origem vira um bloco idêntico de pixels **físicos**, que é a única coisa que o olho vê. O tamanho CSS resultante costuma ser fracionário (1120/3 = 373,33px); **isso está correto, não é bug.**

### Phaser não tem modo de escala inteira — e o `MAX_ZOOM` é armadilha

Os 7 modos são `NONE`, `WIDTH_CONTROLS_HEIGHT`, `HEIGHT_CONTROLS_WIDTH`, `FIT`, `ENVELOP`, `RESIZE`, `EXPAND`. `MAX_ZOOM` é o mais próximo, e tem **dois defeitos desqualificantes**: arredonda em pixels CSS, e é calculado **uma vez** em `parseConfig()` e nunca recalculado — não atualiza em mudança de orientação nem quando a barra de endereço do iOS recolhe. A issue #6629 do Phaser sobre isso foi **fechada sem comentário de mantenedor e sem PR**.

**Receita a usar (calcular na mão):**

```js
const GAME_W = 240, GAME_H = 160;

new Phaser.Game({
  type: Phaser.WEBGL,
  width: GAME_W, height: GAME_H,
  parent: 'game-root',
  render: { pixelArt: true, roundPixels: true },   // roundPixels NÃO é mais padrão no v4
  scale: {
    mode: Phaser.Scale.NONE,           // nós controlamos o tamanho
    autoCenter: Phaser.Scale.CENTER_BOTH,
    autoRound: false,                  // OBRIGATÓRIO false — senão destrói o tamanho fracionário
    resizeInterval: 100,               // padrão 500ms é lento demais para a barra do iOS
    expandParent: false                // padrão true força height:100% no html/body e briga com svh
  }
});

function fit() {
  const dpr = window.devicePixelRatio || 1;
  const el = document.getElementById('game-root');
  const s = Math.max(1, Math.min(
    Math.floor(el.clientWidth  * dpr / GAME_W),
    Math.floor(el.clientHeight * dpr / GAME_H)
  ));
  game.scale.setZoom(s / dpr);         // zoom CSS fracionário, escala de dispositivo inteira
}
window.addEventListener('resize', fit);
window.visualViewport?.addEventListener('resize', fit);
screen.orientation?.addEventListener('change', () => setTimeout(fit, 250));
fit();
```

Cascata de CSS obrigatória (o último válido vence):
```css
canvas {
  image-rendering: -webkit-optimize-contrast;
  image-rendering: -moz-crisp-edges;
  image-rendering: crisp-edges;
  image-rendering: pixelated;   /* 96,35% de suporte global */
}
```
Usar `pixelated`, nunca `crisp-edges` sozinho — há issue aberta no caniuse (#2052, desde 2015) sugerindo que o Safari aceita `crisp-edges` em `<canvas>` **sem de fato pixelizar**.

### O que quebra no celular

- **Barra de endereço:** usar `100svh`, **nunca** `100dvh`. Com `dvh`, a escala inteira oscila 6×→7×→6× toda vez que a barra desliza.
- **Notch / Dynamic Island:** `viewport-fit=cover` é **obrigatório** — sem ele todo `env(safe-area-inset-*)` resolve para `0px`.
- **`ScreenOrientation.lock()` não funciona no iOS Safari** (bug WebKit #257695). `scaleManager.lockOrientation()` é no-op no iPhone.
- **`user-scalable=no` é ignorado no iOS Safari 10+** (decisão de acessibilidade). Funciona apenas em PWA instalado.
- **Bug de timing do `orientationchange` no iOS:** `window.innerWidth/Height` frequentemente ainda trazem valores pré-rotação. Remedir após ~250ms. **Causa nº1 do "fica errado até eu girar duas vezes".**
- Pull-to-refresh: `overscroll-behavior: none` + `body { position: fixed; inset: 0; overflow: hidden }` no iOS.

### Recomendação: **retrato primeiro**

240×160 é 3:2; paisagem de celular é ~2,17:1 — a **altura é sempre a restrição**, então sobram barras laterais (pillarbox), nunca superiores. Transforme isso em recurso: as barras são onde ficam os controles de toque.

Mas o padrão deve ser **retrato**. No iPhone 15 em retrato, `floor(1179/240) = 4×` → 960×640 px de dispositivo, usando 81% da largura e só 25% da altura — sobram ~590px CSS abaixo do campo de jogo para um D-pad decente. Melhor ergonomia que polegares sobre a tela, fiel ao formato do próprio GBA, e **você não consegue forçar rotação no iOS de qualquer forma** (muitos usuários têm trava de rotação ligada e fisicamente não conseguem obedecer). Suportar paisagem trocando o **layout dos controles**, nunca a resolução lógica.

## A.4 Texto — dividir por função, não por estética ✅

**Aqui é onde a estética e o produto realmente colidem**, e onde a pesquisa empurra mais forte contra fidelidade literal.

### O achado crítico para português

**Os charsets nativos de `RetroFont` do Phaser (`TEXT_SET1`–`TEXT_SET11`) não têm um único caractere acentuado.** São ASCII puro. Para `á à â ã é ê í ó ô õ ú ç` é preciso autorar charset customizado — não há atalho.

Além disso: `this.load.bitmapFont()` aceita **apenas XML** (AngelCode BMFont / Glyph Designer / Littera), não JSON.

### Fonte recomendada: **Pixeloid**

| Fonte | Acentos | Licença |
|---|---|---|
| **Pixeloid** (Sans / Sans Bold / Mono) | **1.141 glifos, 135 idiomas** — latim acentuado completo | **SIL OFL 1.1** |
| m5x7 | "muitas letras acentuadas" | CC0 |
| m6x11 | não documentado — **verificar antes de adotar** | livre c/ atribuição |
| Press Start 2P / Silkscreen | **não verificado**; impróprias para parágrafo | OFL |

Pixeloid é a única com cobertura acentuada documentada e abrangente. Gerar o XML com **`msdf-bmfont-xml`**, passando charset customizado.

### A divisão obrigatória

| Onde | O quê | Como |
|---|---|---|
| **Dentro do canvas** | nomes, diálogo curto (≤3 linhas), números de dano, rótulos de menu, HUD | `BitmapText` com Pixeloid |
| **DOM/React sobre o canvas** | **enunciado da questão, alternativas, artigo de lei, explicação, feedback** | resolução do dispositivo, estilizado para combinar |

**O argumento que torna isso quase inegociável para edtech:**

- **WCAG 2.2 SC 1.4.10 (Reflow)** isenta explicitamente *"jogos"* — mas a isenção cobre o **canvas do jogo**, não a interface de leitura de enunciado e artigo de lei.
- **WCAG 2.2 SC 1.4.4 (Resize Text)** exige texto escalável a **200% sem perda de conteúdo ou função**. O documento de entendimento observa que *"imagens de texto não escalam bem porque tendem a pixelizar"*. **Texto rasterizado em canvas é, funcionalmente, imagem de texto — uma UI de leitura só-canvas não consegue satisfazer o 1.4.4.**

Texto em DOM também é selecionável, copiável, lido por leitor de tela e traduzível — coisas que quem estuda texto jurídico denso realmente usa.

**Nunca renderizar um parágrafo de lei brasileira a 8px num canvas de 240×160.** A autenticidade do GBA é um *visual*, não uma meta de legibilidade: a tela do GBA tinha 2,9 polegadas a 30cm do rosto; um celular a distância de braço é outra situação de leitura.

**Guarde o visual** estilizando a camada DOM: Pixeloid Sans a 18px ou 36px (a grade pixel-perfect dela), moldura de diálogo estilo GBA como `border-image` CSS de 9 fatias, e a paleta do jogo.

⚠️ **Autore o charset com todos os acentos, maiúsculos e minúsculos, ANTES de gerar qualquer fonte bitmap.** Adicionar glifo depois obriga a regerar todos os atlas.

## A.5 Entrega mobile — PWA → TWA → Capacitor ✅

### A restrição central

**Um PWA não dispara lembrete diário sem servidor.** As duas APIs candidatas estão mortas ou inúteis:

- **Notification Triggers está encerrada.** Documentação do próprio Chrome: *"O desenvolvimento da API Notification Triggers terminou. Não ficou claro que poderíamos oferecer experiências consistentes e confiáveis entre plataformas."*
- **Periodic Background Sync** é só Chromium, exige PWA instalado, e **o navegador controla a frequência** — você não pode pedir 07:00. Para de disparar exatamente para o usuário que está sumindo.

### iOS, situação em agosto/2026

| | Estado |
|---|---|
| Web push desde | iOS 16.4 (mar/2023) |
| **Ainda exige "Adicionar à Tela de Início"?** | **SIM — inalterado.** Sem push a partir de aba do Safari |
| Declarative Web Push | iOS 18.4 — payload JSON, sem service worker |
| iOS 26 | sites na Tela de Início abrem como web app por padrão — reduz atrito, mas o passo manual permanece |
| `beforeinstallprompt` | **não existe no iOS** — é preciso guiar pelo Compartilhar → Adicionar |
| Apple Developer Program | **não exigido** para web push |

Android Chrome faz push **em aba comum, sem instalar**. Essa assimetria é o problema inteiro.

**Mercado brasileiro (StatCounter, jul/2026): Android 77,59% / iOS 22,41%.** iOS subindo — não tratar como detalhe.

### Caminho recomendado, nesta ordem

**Fase 0 — PWA + web push próprio · $0.** Vite + `vite-plugin-pwa` (usar `injectManifest`, não `generateSW`), Workbox para o shell, IndexedDB para o banco de questões e progresso, `web-push` 3.6.7 com chaves VAPID próprias e um cron gratuito. Android funciona na hora. iOS funciona após instalar — então construa um **guia de instalação real** (detectar iOS fora de standalone, animar o fluxo Compartilhar → Adicionar, e só **depois**, dentro do app instalado, pedir permissão de notificação). Chamar `navigator.storage.persist()` no primeiro uso.

**Fase 1 — TWA na Google Play · +$25 uma vez.** Bubblewrap sobre o mesmo PWA. **Armadilha nº1:** a verificação de Digital Asset Links usa a chave de assinatura do APK, e o Play App Signing pode substituí-la — coloque **as duas** impressões SHA-256 no `assetlinks.json`. **Comece cedo o teste fechado:** conta pessoal exige **12 testadores por 14 dias consecutivos** antes da produção. Isso é 3+ semanas de calendário, não de código.

**Fase 2 — Capacitor no iOS · +$99/ano, quando a retenção justificar.** `@capacitor/local-notifications` faz `{ every: 'day', on: { hour: 19, minute: 0 }, repeats: true }` **sem servidor nenhum, offline**. Isso apaga todos os problemas de iOS de uma vez: sem coaching de instalação, sem servidor de push, sem certificado APNs, sem risco de despejo de cache, mais descoberta na App Store. **Os $99 são o que se paga para fechar o maior vazamento do caminho gratuito.**

**Estado estável: $99/ano + $25 uma vez + $0 de infraestrutura de push.**

**Dois "não":** não usar **Tauri** aqui (a própria documentação diz *"não estamos completamente satisfeitos com a experiência de desenvolvimento no momento"* e a action de CI não suporta mobile); e não usar `@capacitor/push-notifications` para lembrete diário — arrasta APNs, edição de `AppDelegate.swift` e projeto Firebase para algo que o `local-notifications` faz de graça.

**Sobre a diretriz 4.2 da App Store** (funcionalidade mínima): risco real mas administrável — 4.2 protege explicitamente o que tem *"valor de entretenimento duradouro"*, e você entrega um jogo com banco de questões offline, streaks e XP. Blindar empacotando todos os assets localmente e funcionando em modo avião no primeiro uso. **Orçar uma rejeição e uma ressubmissão.**

**Nuance de produto para já projetar:** notificação local não conhece estado de servidor. *"Você está a 1 dia de bater seu recorde de 12 dias 🔥"* retém muito mais que *"Hora de estudar."* Se mensagem personalizada de streak virar central, manter o servidor de push como canal primário nas duas plataformas. **Barato planejar agora, caro adaptar depois.**

---

## Incertezas declaradas da Parte A

1. **Precedentes reais de texto corrido em jogos pixel art** (Undertale, Stardew, Sea of Stars, CrossCode, Papers Please) **não foram verificados** — o orçamento de busca acabou. A recomendação A.4 se apoia no argumento WCAG e nas restrições da API do Phaser, ambos verificados, **não** nesses exemplos. Vale 30 minutos de olhada própria.
2. **`Text.setResolution()` + `pixelArt: true`** — o híbrido de texto nítido dentro do canvas. A aritmética fecha; não há documentação nem exemplo confirmando. **Prototipar antes de depender.**
3. **`image-rendering: pixelated` em `<canvas>` no Safari** — se aparecer borrão no iOS, trocar a arquitetura de canvas (backing store pequeno → 1:1 com pixels de dispositivo) e a questão some.
4. Cobertura de acentos de m6x11, Silkscreen, Press Start 2P e do pacote frostyfreeze **não confirmada**. Pixeloid e m5x7 são as confiáveis.
5. Probabilidade de rejeição pela diretriz 4.2 é **raciocínio, não dado medido**.
6. **iOS 27 está em beta** — rechecar em setembro/2026 se muda algo sobre web push ou a exigência de Tela de Início. É o único evento que poderia alterar a recomendação A.5.
7. A análise de concorrentes na loja cobriu **preparatórios de OAB**, não concurso para Técnico Administrativo. Categoria adjacente e análoga, mas **não é o mercado exato** — a leitura útil é estrutural: presença em loja é custo de entrada, não diferencial. *Todos* os concorrentes de OAB exibem aviso de não-afiliação à banca, sinal de que o mercado já aprendeu que sinalização de confiança importa ali.

---

# PARTE B — Repetição espaçada ✅

## B.1 🔴 O achado que derruba a escolha óbvia

**Half-Life Regression do Duolingo (Settles & Meeder, ACL 2016) NÃO funciona com sinal binário.**

O `p` do modelo não é acerto/erro — é **fração**: `p_recall = session_correct / session_seen`. A nota de rodapé 4 do artigo é explícita, e o dataset público define o campo assim. A meia-vida "observada" é derivada por `h = −Δ / log₂(p)`, que **degenera em p ∈ {0,1}** (log₂(1) = 0 → divisão por zero). A implementação de referência só escapa disso com *clipping* — gambiarra, não solução.

Ou seja: **HLR só se aplica se a sessão perguntar o mesmo conceito 3–5 vezes**, gerando p = 0,8 naturalmente. Se cada questão for um evento binário, HLR está fora.

Números que valem carregar: AUC do HLR foi **0,538** (mal acima do acaso — o Leitner ganhou em AUC com 0,542). O ganho de 45% é só em MAE. E o famoso "+12% de engajamento" do abstract é HLR-**sem**-features-lexicais contra HLR-com — não HLR contra Leitner, que ficou plano e com queda significativa em sessões de prática.

**O próprio Duolingo abandonou.** Birdbrain V1 (2020) é logística/IRT com SGD online estilo Elo — **sinal binário nativo**; V2 (maio/2022) é LSTM comprimindo o histórico em vetor de 40 dimensões, atualizado a cada exercício por "acertou ou não". *(Inferência forte a partir de artigo dos próprios autores na IEEE Spectrum, fev/2023 — não há declaração formal de descontinuação nem paper revisado do Birdbrain.)*

## B.2 ✅ Recomendação para o MVP: mapear binário em 2 pontos de uma escala de 4

`errou → grade 1 (Again/Esqueci)` · `acertou → grade 3 (Good/Lembrei com esforço)` · **nunca emitir 2 ou 4.**

É exatamente o que RemNote e Anki fazem em cartão de digitar resposta, documentado literalmente: *"o `Forgot` ou `Recalled with effort` será selecionado, dependendo se sua resposta bateu ou não"*. Vantagens: usa **FSRS ou SM-2 de prateleira**, sem inventar modelo; e a perda de informação é menor do que parece — a própria documentação do RemNote diz que humanos escolhem grade 3 em **75%+ dos casos**.

**FSRS v6:** 17 parâmetros; exige ~1.000 revisões com pesos padrão antes de otimizar; ~20–30% menos revisões para a mesma retenção que SM-2. **SM-2** (mais simples, bom para começar): guardar por par (usuário, questão) → `easiness` (início 2,5; piso 1,3), `interval`, `repetitions`, `due_date`, `lapses`.

Alternativa se você quiser modelo próprio depois: **logística com histórico ordenado**, como o Quizlet (2017) — AUROC **0,815**, com 4 features: histórico ordenado de 3 respostas, tempo desde a última, tempo entre as duas anteriores, e direção. Achado importante: **a ordem importa** — "errou, errou, acertou" é muito diferente de "acertou, errou, errou" — e as contagens agregadas do HLR não conseguem representar isso.

## B.3 🔴 Dois achados que atingem diretamente as decisões D4 e D5

### Chute contamina o sinal binário — ataca a D4

Múltipla escolha de 4 alternativas embute **25% de piso de falso-positivo**; verdadeiro/falso, 50%. O Quizlet mediu isso e reagiu: **removeu V/F e "nenhuma das anteriores"**, e passou a usar MC apenas como andaime inicial, exigindo **recall guiado** (digitado ou autoavaliado) para um item "se formar".

**Consequência para o Edital Quest:** questões CESPE são certo/errado (50% de chute) e FGV/FCC são múltipla escolha (20–25%). O sinal de combate é ruidoso por construção. A D4 ("cola vira autossabotagem") depende de um sinal que sabe distinguir domínio de sorte — vale desenhar um formato de recall guiado para o item "graduar".

### Prática precisa ser separada do agendamento — ataca a D5

O WaniKani mantém um modo "Extra Study" cujos resultados **explicitamente não afetam o progresso**. Sem essa muralha, *cramming* corrompe o modelo.

**Consequência para as Dungeons de Reciclagem:** se a masmorra opcional alimentar o agendador, o jogador que farma revisão fácil **corrompe a própria agenda** — e o R-02 (revisão opcional pode não acontecer) ganha um irmão pior: revisão opcional pode acontecer *errado*. Regra a levar ao GDD: **só revisão agendada muda o estado do SRS.**

## B.4 Fórmulas de referência (escadas binárias puras)

**WaniKani** — 9 estágios, resposta digitada, sem botão de pular, sem autoavaliação:
```
novo_estágio = estágio − ceil(erros/2) × (2 se estágio ≥ 5, senão 1)     piso = 1
```
Intervalos: 4h · 8h · 1d · 2d · 1sem · 2sem · 1mês · 4meses. *Detalhe de implementação copiável:* os valores reais são **1 hora a menos** que o nominal (23h, 47h, 167h…) e arredondados para o início da hora — evita deriva de horário.

**Memrise** — escada fixa 4h → 12h → 24h → 6d → 12d → 48d → 96d → 6 meses, com **reset total** ao errar.

---

# PARTE C — Origem das questões e conformidade ✅

> Levantamento factual do que fontes públicas dizem. **Não é parecer jurídico** e não contém veredito sobre legalidade.

## C.1 A única decisão judicial diretamente sobre o tema

**TJSP, Apelação 1112376-68.2021.8.26.0100**, 3ª Câmara de Direito Privado, Rel. Des. Carlos Alberto de Salles. Associações de certificação processaram um curso preparatório por reusar questões. **Recurso negado, improcedência mantida:**

> *"a elaboração de questões consiste em nada mais do que um método de estudo ou avaliação de determinado conhecimento científico, faltando-lhe o indispensável requisito de originalidade"*

O tribunal rejeitou **as duas** teses: questão individual (sem originalidade) e a coletânea como base protegida (*"não apresentava um critério formal de organização singular"*).

**Ressalvas que impedem tratar isso como resolvido:**
- Foi **por maioria**, não unânime.
- Tratava de **certificação privada**, não de concurso público.
- É **câmara estadual**, não STJ nem STF. Nenhuma decisão do STJ encontrada; não foi possível verificar se houve recurso especial.
- O acórdão se apoiou no **art. 8º, I** (*métodos*), **não** no art. 8º, IV (*atos oficiais*). A tese do "ato oficial" segue **não testada**.

**Contraste relevante:** tribunais **protegem** material didático compilado — TJES condenou copiadora por reproduzir apostila de cursinho; Gran Cursos venceu caso análogo no TJDFT. A costura é entre *questão* e *compilação comentada*.

## C.2 ✅ O achado mais acionável: Cebraspe

**As provas do Cebraspe trazem permissão expressa impressa:**

> *"É permitida a reprodução deste material apenas para fins didáticos, desde que citada a fonte."*

Confirmado em **6 documentos distintos** (PF 2021 cargos 1 e 2, PC-DF 2019, PGDF 2019, Pref. Barra dos Coqueiros 2020), incluindo arquivo de gabarito com justificativas. O Cebraspe **não tem termos de uso no site**.

**E há API pública sem autenticação:**
```
apis.cebraspe.org.br/cebraspe/eventos/tipo/concursos/   → 425 concursos encerrados (2014–2026)
apis.cebraspe.org.br/cebraspe/eventos/{ID}              → PDFs em cdn.cebraspe.org.br
```
Sem login, sem limite de taxa observado. Em 14 concursos amostrados, 10 tinham prova objetiva baixável.

⚠️ **Duas ressalvas:** arquivos publicados em 2024–2025 começam no bloco de instruções **sem a capa**, então não foi possível confirmar que o aviso ainda aparece nas provas atuais. E "fins didáticos" aplicado a **app pago** é justamente uma das perguntas para advogado.

**As outras bancas:**

| Banca | Postura |
|---|---|
| **Cebraspe** | permissão expressa para fins didáticos com citação da fonte |
| **FGV** | termos reservam todos os direitos e **proíbem acesso automatizado** — restrição **contratual**, que pode vincular mesmo se o conteúdo não for protegível |
| **FCC** | **não é público** — exige nº de inscrição + código do cartão-resposta |
| **VUNESP** | nenhum aviso |
| **Objetiva** | todos os direitos reservados, reprodução proibida |

## C.3 Como os incumbentes operam

Qconcursos, TEC, Gran e Estratégia são **completamente silenciosos** sobre base legal para republicar questões de banca — zero ocorrências de *domínio público*, *atos oficiais* ou art. 8º nos termos. O que reivindicam é a camada de **comentário, classificação e base de dados**, nunca as questões. Nenhuma ação de banca contra eles foi encontrada. O Qconcursos operou de ~2008 e foi **adquirido pela Yduqs por R$ 208 milhões em 2021** — transação que normalmente envolveria diligência jurídica exatamente sobre isso.

*(Cuidado ao considerar essas fontes: TEC e Estratégia proíbem contratualmente scraping, mineração e uso em IA, inclusive RAG. Gran não tem cláusula; Qconcursos bloqueia no edge.)*

**Sem *fair use* no Brasil.** O art. 46 é lista fechada, e o inciso VIII só vale *"quando a reprodução em si não seja o objetivo principal da obra nova"* — num banco de questões, a reprodução **é** o produto.

## C.4 🔴 LGPD — e o achado que muda o quadro

**Dado de desempenho de estudo NÃO é sensível.** O art. 5º, II é lista fechada de nove categorias; resposta, acerto, tempo e streak não estão em nenhuma.

### ⚠️ ECA Digital já está em vigor

**Lei 15.211/2025**, em vigor desde **17/03/2026**, regulamentada pelo **Decreto 12.880/2026**, com **ANPD fiscalizando**. Aplica-se a *"todo produto ou serviço direcionado a crianças e a adolescentes ou **de acesso provável por eles**"*.

**Decreto 12.880, art. 9º, §único, proíbe expressamente:**
- **III — "a oferta de recompensas pelo tempo de uso"**
- **IV — "o aparecimento de notificações excessivas"**

**Streak + notificação push cai diretamente nos incisos III e IV.** Sanções do art. 35: até 10% do faturamento, ou — sem faturamento — **R$ 10 a R$ 1.000 por usuário cadastrado**, teto R$ 50M. É a fórmula por usuário que machuca um app pré-receita.

Contexto de fiscalização: em **25/08/2026 a ANPD multou a ByteDance/TikTok em R$ 153,7 milhões** por tratar dados de crianças e adolescentes sem base legal válida — maior multa LGPD até hoje, exatamente por esse modo de falha.

### ✅ Regime simplificado — e a armadilha

**Resolução CD/ANPD nº 2/2022.** Desenvolvedor solo qualifica (art. 2º, I cobre pessoas naturais). Dispensa **encarregado/DPO** (art. 11 — basta canal de contato publicado), permite **ROPA simplificado** (modelo de uma página da ANPD), e **dobra prazos** (declaração completa 15 → 30 dias).

⚠️ **A armadilha (art. 4º):** perde-se o regime ao atender **um critério geral E um específico**. O critério específico **(d) — "dados pessoais de crianças, de adolescentes"** é atendido **automaticamente** no momento em que houver usuário menor.

> **Isto reforça fortemente a Decisão D3/pergunta 4: MVP é CONCURSO (adultos), vestibular fora.** Manter menores fora preserva o regime simplificado e evita a exposição do ECA Digital de uma vez só.

**Piso realista de conformidade:** política de privacidade com os 7 itens do art. 9º e menção expressa aos direitos do art. 18; base legal escolhida e documentada por finalidade; ROPA simplificado; mecanismo de acesso e exclusão gratuito dentro do art. 19 (simplificado **imediato**, completo em 30 dias); canal de contato publicado; checklist de segurança da ANPD; registro interno de incidentes por 5 anos.

## C.5 Onde advogado é genuinamente necessário

1. **Se questões de concurso são protegidas.** Uma decisão de câmara estadual, por maioria, sobre certificação privada. Sem STJ. **É a incerteza que sustenta o projeto inteiro.**
2. Se prova de banca **privada** é "ato oficial" do art. 8º, IV — não testado.
3. Se a permissão "fins didáticos" do Cebraspe cobre **app pago**, e se ainda vale para provas pós-2023.
4. Uso comercial de **nome de banca** — art. 132, IV da Lei 9.279/96 tem a ressalva *"sem conotação comercial"*.
5. Alcance do **art. 24 do ECA Digital** (vínculo com responsável até 16 anos) a app de estudo que não é rede social — conflito entre o título do capítulo e o texto do caput.
6. **Se streak + notificações violam** o art. 9º, §único, III e IV do Decreto 12.880.
