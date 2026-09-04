# 02 — Maturação

Onde uma ideia bruta é submetida a pesquisa, corte de escopo e análise de risco até virar um documento aprovável (GDD para jogos, PRD para software).

## Estrutura de cada ideia em maturação

```
02-maturacao/<slug>/
├── 00-ficha.md          ← a ficha original vinda de 01-ideias-brutas (status: maturando)
├── 01-pesquisa.md       ← referências, concorrentes, tecnologia, viabilidade
├── 02-escopo.md         ← MVP vs. visão completa; o que fica de fora e por quê
├── 03-riscos.md         ← técnicos, de escopo, de motivação; mitigação de cada um
└── 04-gdd.md | 04-prd.md ← o documento candidato à aprovação
```

Os arquivos 01–04 são gerados aplicando o molde de incubação correspondente em `_templates/`. O molde conduz o Diretor Geral por perguntas; as respostas do CEO alimentam os documentos.

## Critérios para sair da maturação

Uma ideia só pode ser proposta para aprovação quando:

- [ ] O MVP cabe em uma frase e tem **no máximo 3 funcionalidades centrais**.
- [ ] Existe uma stack decidida com justificativa escrita.
- [ ] Cada risco listado tem mitigação ou aceitação explícita.
- [ ] O documento final (GDD/PRD) tem critérios de "pronto" mensuráveis para o MVP.
- [ ] O Diretor Geral emitiu **parecer** (recomenda / recomenda com ressalvas / não recomenda) no rodapé do documento.

## Saída

- **Aprovada pelo CEO** → o `04-gdd.md`/`04-prd.md` é copiado para `../03-gdds-aprovados/<slug>.md` com `status: aprovada`. A pasta de maturação permanece como histórico.
- **Reprovada** → `status: arquivada` na ficha; pasta permanece.
