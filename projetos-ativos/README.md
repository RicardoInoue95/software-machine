# projetos-ativos

Cada subpasta aqui é um **projeto independente**: repositório git próprio, `CLAUDE.md` próprio, equipe de sub-agentes própria.

> **Nota histórica:** esta pasta se chamava `[projetos-ativos]`. Os colchetes quebravam padrões glob (`[...]` é classe de caracteres), inutilizando qualquer regra de `.gitignore`, CI ou script que mirasse a pasta. Renomeada em 2026-08-25.

## Regras

- Só nasce projeto aqui via molde de boot (`_templates/boot-projeto-*.md`), e só com **GDD/PRD aprovado + blueprint assinado** em `_planning/03-gdds-aprovados/`.
- **Trabalhe sempre de dentro da pasta do projeto.** Abra o Claude em `projetos-ativos/<slug>/`, nunca na raiz, para sessões de desenvolvimento.
- Nenhum projeto importa código de outro. Componente compartilhado vira projeto próprio (biblioteca).
- A raiz da Fábrica não versiona nada daqui; cada projeto cuida do seu `.git` e do seu remote.
- Código exploratório sem compromisso **não vem para cá** — vai para `_spikes/`. Ver `_spikes/README.md`.

## Projetos

| Slug | Tipo | Bootado em | Equipe | Estado |
|---|---|---|---|---|
| [`edital-quest`](edital-quest/) | jogo (web/PWA) | 2026-08-27 | 4 agentes | estrutura pronta, sem código de aplicação |
