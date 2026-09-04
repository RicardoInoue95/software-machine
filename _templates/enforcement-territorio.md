# MOLDE: Enforcement de Território

> Uso: passo 7 dos moldes de boot. Transforma a matriz de território do blueprint em **parede executável**, não em pedido educado.
> Gera três arquivos dentro de `projetos-ativos/<slug>/.claude/`.

---

## O que é parede e o que continua sendo pedido

**Sinceridade primeiro**, porque prometer garantia que não existe é pior do que não ter garantia.

| Limite | Mecanismo | É parede? |
|---|---|---|
| Documento aprovado é imutável (`docs/PRD.md`, `docs/GDD.md`, `docs/BLUEPRINT.md`) | regra `deny` em `settings.json` | **Sim** — bloqueio duro |
| Segredos nunca lidos nem escritos (`.env`) | regra `deny` em `settings.json` | **Sim** — bloqueio duro |
| Agente só escreve no próprio território | hook `PreToolUse` + `territorio.json` | **Sim** — o hook lê `agent_type` e nega |
| Agente só usa certas ferramentas | `tools:` / `disallowedTools:` no frontmatter do agente | **Sim** |
| Gatilhos de escopo (não mudar MVP, não adicionar dependência) | texto no prompt do agente | **Não** — instrução |

Não existe campo de frontmatter que limite caminhos de escrita por agente. O que existe é o hook `PreToolUse`, que **recebe a identidade do subagente** (`agent_type`) junto com o caminho do arquivo e pode negar a chamada. É por isso que o território vira `territorio.json` + script, e não uma linha no `.md` do agente.

Precedência real: `deny` → `ask` → `allow`, primeira regra que casar vence. Um hook que sai com código 2 nega mesmo que uma regra `allow` permitisse. Um `deny` bloqueia mesmo que o hook aprove.

---

## Arquivo 1 — `.claude/settings.json`

Versionado, vale para todos. Preencher `deny` conforme a stack.

```json
{
  "permissions": {
    "deny": [
      "Read(.env)",
      "Read(.env.*)",
      "Edit(.env)",
      "Write(.env)",
      "Edit(docs/PRD.md)",
      "Write(docs/PRD.md)",
      "Edit(docs/GDD.md)",
      "Write(docs/GDD.md)",
      "Edit(docs/BLUEPRINT.md)",
      "Write(docs/BLUEPRINT.md)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write|NotebookEdit",
        "hooks": [
          {
            "type": "command",
            "command": "powershell -NoProfile -ExecutionPolicy Bypass -File .claude/hooks/territorio.ps1"
          }
        ]
      }
    ]
  }
}
```

**Por que negar edição dos próprios documentos aprovados:** eles carregam as duas assinaturas do CEO. Mudança de escopo se registra em `docs/CHANGELOG-ESCOPO.md` — que é gravável — e o documento assinado permanece congelado. Isso torna a Fase 3/4 estruturalmente irreversível de dentro da oficina.

Manter apenas linhas de `deny` para arquivos que **existem** naquela stack: num projeto de GBA não há `.env`, mas há `docs/GDD.md`.

---

## Arquivo 2 — `.claude/hooks/territorio.json`

A matriz de território do blueprint, em forma legível por máquina. Prefixo terminado em `/` é pasta (vale para tudo abaixo); sem `/` é arquivo exato.

```json
{
  "backend-dev":   ["src/server/", "src/lib/", "prisma/"],
  "frontend-dev":  ["src/app/", "src/components/"],
  "qa-engineer":   ["tests/"],
  "product-owner": ["docs/CHANGELOG-ESCOPO.md", "docs/ADR/"]
}
```

Regras de preenchimento:

- **Um caminho, um dono.** Se dois agentes aparecem com o mesmo prefixo, o blueprint está errado — pare e escale.
- Agente **ausente** deste arquivo não é limitado pelo hook (ex.: um `security-reviewer` que só lê).
- A sessão principal (você e o Claude orquestrador, sem subagente) **não** é limitada por este hook — `agent_type` vem vazio.

---

## Arquivo 3 — `.claude/hooks/territorio.ps1`

```powershell
# Enforcement de território — gerado no boot a partir do blueprint.
# Nega Edit/Write de subagente fora do território declarado em territorio.json.
$ErrorActionPreference = 'Stop'

$raw = [Console]::In.ReadToEnd()
if ([string]::IsNullOrWhiteSpace($raw)) { exit 0 }

try { $evt = $raw | ConvertFrom-Json } catch { exit 0 }

# Sessão principal (sem subagente) não é limitada aqui.
$agente = $evt.agent_type
if ([string]::IsNullOrWhiteSpace($agente)) { exit 0 }

$arquivo = $evt.tool_input.file_path
if ([string]::IsNullOrWhiteSpace($arquivo)) { exit 0 }

$mapaPath = Join-Path $PSScriptRoot 'territorio.json'
if (-not (Test-Path -LiteralPath $mapaPath)) { exit 0 }
$mapa = Get-Content -LiteralPath $mapaPath -Raw | ConvertFrom-Json

# Agente sem território declarado não é limitado por este hook.
if ($mapa.PSObject.Properties.Name -notcontains $agente) { exit 0 }

# Caminho relativo à raiz do projeto (.claude/hooks -> sobe dois níveis).
$raiz = Split-Path (Split-Path $PSScriptRoot -Parent) -Parent
$rel = $arquivo
if ($arquivo.StartsWith($raiz, [System.StringComparison]::OrdinalIgnoreCase)) {
    $rel = $arquivo.Substring($raiz.Length)
}
$rel = ($rel -replace '\\', '/').TrimStart('/')

$permitidos = @($mapa.$agente)
foreach ($p in $permitidos) {
    $pn = (($p -replace '\\', '/').TrimStart('/'))
    if ($pn.EndsWith('/')) {
        if ($rel.StartsWith($pn, [System.StringComparison]::OrdinalIgnoreCase)) { exit 0 }
    }
    elseif ($rel -ieq $pn) { exit 0 }
}

$lista = $permitidos -join ', '
[Console]::Error.WriteLine("BLOQUEIO DE TERRITORIO: o agente '$agente' nao escreve em '$rel'.")
[Console]::Error.WriteLine("Territorio dele: $lista")
[Console]::Error.WriteLine("Isto e gatilho de bloqueio do blueprint. PARE, nao contorne, e escale ao CEO.")
exit 2
```

Código de saída **2** com mensagem em stderr = chamada negada, e o texto do stderr volta para o agente — por isso a mensagem já diz o que ele deve fazer (parar e escalar), em vez de só recusar.

---

## Verificação obrigatória no boot

**Não dar o boot por concluído sem rodar esta suíte.** Um hook que nunca nega é pior do que hook nenhum: dá sensação de proteção sem proteger.

Colar na raiz do projeto, ajustando os nomes de agente e caminhos ao `territorio.json` real:

```powershell
$h = '.claude/hooks/territorio.ps1'
$casos = @(
  @{ n='1 invasao de territorio        '; j='{"agent_type":"frontend-dev","tool_input":{"file_path":"src/server/db.ts"}}'; esperado=2 },
  @{ n='2 territorio proprio           '; j='{"agent_type":"frontend-dev","tool_input":{"file_path":"src/app/page.tsx"}}'; esperado=0 },
  @{ n='3 caminho absoluto             '; j='{"agent_type":"qa-engineer","tool_input":{"file_path":"C:\\proj\\src\\app\\x.tsx"}}'; esperado=2 },
  @{ n='4 segunda pasta do territorio  '; j='{"agent_type":"backend-dev","tool_input":{"file_path":"prisma/schema.prisma"}}'; esperado=0 },
  @{ n='5 arquivo exato permitido      '; j='{"agent_type":"product-owner","tool_input":{"file_path":"docs/CHANGELOG-ESCOPO.md"}}'; esperado=0 },
  @{ n='6 arquivo vizinho negado       '; j='{"agent_type":"product-owner","tool_input":{"file_path":"docs/PRD.md"}}'; esperado=2 },
  @{ n='7 sessao principal sem agente  '; j='{"tool_input":{"file_path":"qualquer/coisa.ts"}}'; esperado=0 },
  @{ n='8 agente sem territorio        '; j='{"agent_type":"security-reviewer","tool_input":{"file_path":"src/server/db.ts"}}'; esperado=0 },
  @{ n='9 barras invertidas            '; j='{"agent_type":"frontend-dev","tool_input":{"file_path":"src\\components\\btn.tsx"}}'; esperado=0 },
  @{ n='10 json invalido               '; j='nao-e-json'; esperado=0 }
)
foreach ($c in $casos) {
  $c.j | powershell -NoProfile -ExecutionPolicy Bypass -File $h 2>$null | Out-Null
  $got = $LASTEXITCODE
  $ok = if ($got -eq $c.esperado) { 'PASS' } else { 'FALHOU' }
  Write-Output ("{0}  esperado={1} obtido={2}  {3}" -f $c.n, $c.esperado, $got, $ok)
}
```

**Os 10 precisam dar PASS.** Os casos 7, 8 e 10 são os que garantem que o hook *não* atrapalha quem não deveria bloquear — hook paranoico trava o projeto inteiro e você acaba desligando o enforcement, que é o pior desfecho possível.

Esta suíte foi executada e passou 10/10 na fundação da Fábrica em 2026-08-25. Mensagem de bloqueio devolvida ao agente no caso 1:

```
BLOQUEIO DE TERRITORIO: o agente 'frontend-dev' nao escreve em 'src/server/db.ts'.
Territorio dele: src/app/, src/components/
Isto e gatilho de bloqueio do blueprint. PARE, nao contorne, e escale ao CEO.
```

---

## Portabilidade

O script acima é PowerShell porque a Fábrica roda em Windows. Portar para Linux/macOS = mesma lógica em `sh` + `jq`, trocando a linha `command` do `settings.json` para `.claude/hooks/territorio.sh`. O `territorio.json` não muda.

---

## Frontmatter do agente — a camada complementar

No `.claude/agents/<nome>.md`, restringir ferramentas fecha o que o hook não cobre:

```yaml
---
name: security-reviewer
description: Revisa auth, dados pessoais e segredos antes do merge
tools: Read, Grep, Glob          # não escreve nada
---
```

```yaml
---
name: qa-engineer
description: Escreve e roda testes automatizados
tools: Read, Edit, Write, Grep, Glob, Bash
---
```

Agente que só lê (`security-reviewer`) não precisa entrar no `territorio.json` — o `tools:` já resolve.
