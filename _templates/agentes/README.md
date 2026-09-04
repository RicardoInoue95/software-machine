# Biblioteca de Arquétipos de Agentes

Esqueletos dos papéis mais comuns, prontos para adaptar no boot. Reduzir o boot a poucos passos é o propósito desta pasta.

> **Leia antes:** a *Doutrina de agentes* no `CLAUDE.md` da raiz. O ponto essencial: especializar **não** subtrai capacidade. Todo agente é o mesmo modelo com o mesmo conhecimento técnico. A seção "Você conhece profundamente" **direciona prioridade**, não concede saber.

## Catálogo

| Arquétipo | Tipo | Escreve? | Papel em uma linha |
|---|---|---|---|
| [`game-designer`](game-designer.md) | jogo | sim | Guardião do GDD: balanceamento, conteúdo, coerência de design |
| [`engine-dev`](engine-dev.md) | jogo | sim | Sistemas do jogo em código; o core loop funcionando |
| [`platform-specialist`](platform-specialist.md) | jogo | sim | Restrições do hardware/toolchain; performance como feature |
| [`asset-pipeline`](asset-pipeline.md) | jogo | sim | Conversão e organização de arte e áudio |
| [`product-owner`](product-owner.md) | software | sim | Guardião do PRD: backlog, critérios de aceite |
| [`backend-dev`](backend-dev.md) | software | sim | API, banco, regras de negócio |
| [`frontend-dev`](frontend-dev.md) | software | sim | Interface, UX, acessibilidade |
| [`devops`](devops.md) | software | sim | CI/CD, ambientes, deploy, observabilidade |
| [`qa`](qa.md) | ambos | sim | Testes, regressão, critérios de pronto verificados |
| [`security-reviewer`](security-reviewer.md) | ambos | **não** | Auth, dados pessoais, segredos, LGPD |

`_ARQUETIPO-TEMPLATE.md` é a forma canônica, para papéis fora do catálogo.

## Como usar no boot

1. A Seção 2 do blueprint já decidiu **quais** papéis existem. O catálogo não decide equipe — o blueprint decide.
2. Para cada papel, copiar o arquétipo para `projetos-ativos/<slug>/.claude/agents/<nome>.md`.
3. **Adaptar** — os cinco pontos obrigatórios:
   - `<PROJETO>`, `<STACK>` e afins substituídos pelos valores reais
   - **Área de atuação** com os caminhos reais da árvore aprovada
   - **Gatilhos de bloqueio** com os específicos do projeto somados aos seis universais
   - **Interface de comunicação** com os nomes reais dos outros agentes da equipe
   - "Você conhece profundamente" ajustado à stack real (não à genérica do arquétipo)
4. Alimentar `territorio.json` com as áreas de atuação (ver `_templates/enforcement-territorio.md`).

**Arquétipo copiado sem adaptação produz agente genérico** — que age com confiança sobre um projeto que não conhece. É pior do que não ter o agente.

## Regras da biblioteca

- **Equipe de 3 a 6.** O catálogo tem 10 arquétipos; nenhum projeto usa todos. Blueprint com 8 agentes está errado.
- **Território exclusivo.** Dois agentes nunca com o mesmo caminho. Onde precisariam, o caminho tem dono único e vira fronteira de contrato.
- **Quem só lê não entra no `territorio.json`.** `security-reviewer` é restringido por `tools:` (sem Edit/Write), não por hook.
- **Papel fora do catálogo?** Usar `_ARQUETIPO-TEMPLATE.md`. Se o papel se repetir num segundo projeto, promovê-lo a arquétipo aqui.
