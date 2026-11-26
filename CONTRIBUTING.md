# Guia de Contribuiçêo — INEMA Engenharia Civil

Obrigado por contribuir. Esta plataforma calcula coisas que sustentam estruturas reais,
entêo a barra de qualidade é alta e algumas regras sêo **inegociáveis**. Leia este guia
antes de abrir um cálculo novo.

## Princípio central

> **LLMs nêo fazem contas. Python faz contas. LLMs orquestram, interpretam e documentam.**

Todo número nasce em Python determinístico, é validado (unidades, física, norma) e sai
sempre acompanhado do aviso de responsabilidade técnica.

## Regras inegociáveis (de `CLAUDE.md`)

1. **LLM nunca faz aritmética diretamente.** Todo cálculo numérico vai para Python via MCP.
2. **Todo resultado inclui o aviso de responsabilidade técnica** do engenheiro
   (Lei 6.496/77 / Lei 5.194/66 / Resoluçêo CONFEA/CREA), vindo de `lib/reporting`.
3. **Todo cálculo passa pela camada de validaçêo** (`lib/validators/`) antes de ser
   apresentado — olhe sempre `aprovado`/`validacao` do bundle.
4. **Unidades sêo sempre verificadas com `pint`** antes de qualquer operaçêo.
5. **Abstençêo:** fora da faixa de validade, **levante `ValueError`/`KeyError`** e
   recuse — **nunca invente um número**. O que nêo está em `listar_capacidades` o agente
   recusa.

## Setup

```bash
uv sync --extra dev
```

## Adicionar um novo cálculo (a fatia vertical)

Siga o passo a passo detalhado em
[`docs/11-como-adicionar-skill.md`](docs/11-como-adicionar-skill.md). Em resumo, **por
TDD** (teste antes do código):

1. **Teste primeiro** (`tests/unit/test_<domínio>_<x>.py`) com o **hand-calc no
   comentário citando a norma** (e página/seçêo) e tolerância numérica explícita
   (padrêo 0,1 %). Veja-o falhar antes de implementar.
2. **Núcleo** (`lib/<domínio>/<x>.py`): aritmética determinística, **modelos Pydantic**
   na saída, constantes de faixa no topo e **abstençêo via `ValueError`** fora delas. Sem
   disclaimer e sem efeitos colaterais aqui.
3. **Validador** (`lib/validators/<domínio>_check.py`): `validate_<x>(r) ->
   ValidationReport`, com checagens de **unidades (`pint`: `Q_`/`ensure`)**, física e
   normativa via objetos `Check`. `passed` = nenhuma checagem `fail`.
4. **Memorial** (`lib/reporting/memorial.py`): `render_<x>_memorial(r, rep)` em Markdown,
   **embutindo o `DISCLAIMER`** (é ele que carrega "RESPONSABILIDADE TÉCNICA" e a Lei
   6.496/77) — nunca redija o aviso à mêo.
5. **Orquestrador** (`lib/service.py`): `solve_<x>(...)` costura núcleo → validaçêo →
   memorial e devolve o bundle padrêo via `_bundle(r, rep, memorial)`.
6. **Ferramenta MCP** (`mcp/calc-server/server.py`): `@mcp.tool()` **fina** que só chama
   `solve_<x>`, mais a entrada correspondente em `listar_capacidades`.
7. **SKILL.md do agente** (`agents/<nome>/SKILL.md`): persona que aciona a ferramenta,
   declara escopo ✅/❌, abstençêo e disclaimer — ver
   [`docs/10-como-adicionar-agente.md`](docs/10-como-adicionar-agente.md).
8. **Cobertura E2E**: acrescente um caso **aprovado** ao dicionário `_CASOS_APROVADOS` de
   `tests/integration/test_e2e_dominios.py`.

O catálogo completo do que já existe está em
[`docs/14-catalogo-calculos.md`](docs/14-catalogo-calculos.md).

## O que nêo fazer

- Nêo faça aritmética na ferramenta MCP nem no `solve_*` — o cálculo vive no núcleo.
- Nêo chute valores de norma; use as tabelas em `normas/` (busca via
  `consultar_tabela_norma`) e abstenha-se quando o termo nêo casar.
- Nêo escreva o disclaimer manualmente; reutilize `lib/reporting/disclaimer.py`.
- Nêo introduza relógio/efeitos colaterais escondidos no núcleo — `timestamp` vem do
  chamador (ver `lib/projects/store.py`).

## Portões verdes (rode antes de abrir o PR)

```bash
uv run pytest -q                                      # toda a suíte verde
uv run ruff check lib mcp tests                       # "All checks passed!"
uv run python mcp/calc-server/server.py --selftest    # "SELFTEST OK"
```

Um PR só é considerado pronto quando os três comandos passam, o cálculo novo tem teste
com referência bibliográfica, e o catálogo (`docs/14`) foi atualizado.

## Versionamento

Semver `vX.YY.ZZ`, conforme `CLAUDE.md`: patch incrementa o último dígito; minor
incrementa o do meio **carregando** o último (nêo zera); major zera o resto. Atualize
todos os marcadores de versêo (ex.: `pyproject.toml`) de forma consistente.
