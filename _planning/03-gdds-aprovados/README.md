# 03 — GDDs / PRDs Aprovados

Documentos assinados pelo CEO. Cada projeto tem **dois** arquivos aqui, e o boot exige os dois:

- `<slug>.md` — o GDD/PRD aprovado (**o quê** será construído).
- `<slug>-BLUEPRINT.md` — arquitetura física + organograma de agentes (**como** e **por quem**), produzido na Fase 4 e assinado separadamente.

## Regras

- Um par de arquivos por projeto, com `status: aprovada` (vira `ativa` após o boot).
- O **blueprint só é escrito depois** do GDD/PRD assinado, e **antes** de qualquer arquivo de projeto existir. Ver *Protocolo de Blueprint* no `CLAUDE.md` da raiz.
- O documento é **congelado** no momento da aprovação. Mudanças de escopo depois do boot são registradas no `CLAUDE.md` do projeto e no `docs/CHANGELOG-ESCOPO.md` dele, não aqui.
- O rodapé de cada documento deve conter:
  ```
  ---
  Aprovado pelo CEO em: AAAA-MM-DD
  Parecer do Diretor Geral: recomenda | recomenda com ressalvas | não recomenda (CEO decidiu prosseguir)
  Blueprint assinado em: AAAA-MM-DD | pendente
  Boot realizado em: AAAA-MM-DD | pendente
  ---
  ```

## Próximo passo após a aprovação do GDD/PRD

**Fase 4 — Blueprint.** O Diretor Geral produz `<slug>-BLUEPRINT.md` com:

1. **Arquitetura física** — árvore exata de diretórios, cada um justificado pela stack; fronteiras de contrato; o que não é versionado.
2. **Organograma** — 3 a 6 agentes, cada um com Área de Atuação, Gatilhos de Bloqueio, Interface de Comunicação; matriz de território sem sobreposição; papéis descartados e por quê.

E **para**, submetendo ao CEO. Nada é gerado antes da assinatura.

## Próximo passo após a assinatura do blueprint

**Fase 5 — Boot.** O Diretor Geral aplica `_templates/boot-projeto-jogo.md` ou `_templates/boot-projeto-software.md`, executando o blueprint ao pé da letra:

1. `projetos-ativos/<slug>/` com `git init`.
2. `CLAUDE.md` específico do projeto.
3. `.claude/agents/` com a equipe exata do blueprint.
4. Estrutura de pastas exata da Seção 1 do blueprint.
5. `docs/` com GDD/PRD, BLUEPRINT e CHANGELOG-ESCOPO.
6. Atualização da tabela de Portfólio no `CLAUDE.md` raiz.
