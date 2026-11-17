# INEMA Engenharia Civil â€” Plataforma de Agentes de IA

Plataforma de **agentes de IA para cÃ¡lculos de engenharia civil**, construÃ­da sobre
Claude Code. O agente conversa em linguagem natural, interpreta o problema, escolhe o
mÃ©todo normativo e **delega toda a aritmÃ©tica a uma engine determinÃ­stica em Python** â€”
que valida o resultado e gera o memorial de cÃ¡lculo com o devido aviso de
responsabilidade tÃ©cnica.

> **Estado:** `v1.0.0` â€” Fases 1 a 6 concluÃ­das Â· **359 testes** verdes (334 unitÃ¡rios +
> 25 E2E) Â· `ruff` limpo Â· `--selftest` do MCP server OK Â· 18 domÃ­nios de cÃ¡lculo.

---

## Ãndice

1. [O que Ã©](#o-que-Ã©)
2. [PrincÃ­pio central](#princÃ­pio-central)
3. [Por que existe (escopo e limites)](#por-que-existe-escopo-e-limites)
4. [Arquitetura](#arquitetura)
5. [DomÃ­nios e cÃ¡lculos](#domÃ­nios-e-cÃ¡lculos)
6. [InÃ­cio rÃ¡pido](#inÃ­cio-rÃ¡pido)
7. [Como usar](#como-usar)
8. [Pipeline de validaÃ§Ã£o](#pipeline-de-validaÃ§Ã£o)
9. [Responsabilidade tÃ©cnica](#responsabilidade-tÃ©cnica)
10. [Estrutura do projeto](#estrutura-do-projeto)
11. [Agentes](#agentes)
12. [Testes e qualidade](#testes-e-qualidade)
13. [Como contribuir](#como-contribuir)
14. [LimitaÃ§Ãµes](#limitaÃ§Ãµes)
15. [Roadmap (fases)](#roadmap-fases)
16. [ReferÃªncias normativas](#referÃªncias-normativas)
17. [LicenÃ§a](#licenÃ§a)

---

## O que Ã©

Uma plataforma onde **engenheiros conversam com agentes de IA** para resolver cÃ¡lculos de
engenharia civil â€” concreto armado, fundaÃ§Ãµes, geotecnia, hidrÃ¡ulica, estruturas
metÃ¡licas, pavimentaÃ§Ã£o, terraplenagem, taludes e orÃ§amento. Cada cÃ¡lculo entrega:

- o **resultado estruturado** (dados validados),
- um **relatÃ³rio de validaÃ§Ã£o** automÃ¡tica (4 verificaÃ§Ãµes independentes),
- um **memorial de cÃ¡lculo** em Markdown, passo a passo,
- o **aviso de responsabilidade tÃ©cnica** obrigatÃ³rio.

A IA cuida da parte que ela faz bem â€” **entender o problema, escolher o mÃ©todo, explicar
e documentar**. O nÃºmero em si nunca sai do modelo de linguagem: sai do Python.

## PrincÃ­pio central

> **LLMs nÃ£o fazem contas. Python faz contas. LLMs orquestram, interpretam e documentam.**

Essa nÃ£o Ã© uma preferÃªncia estÃ©tica â€” Ã© a decisÃ£o de arquitetura que torna a plataforma
defensÃ¡vel. A literatura cientÃ­fica de ponta mostra que um LLM calculando "de cabeÃ§a"
Ã© nÃ£o-confiÃ¡vel (confiabilidade pode cair abaixo de 10% em problemas de engenharia
estrutural), enquanto **delegar o cÃ¡lculo a uma ferramenta determinÃ­stica** leva a
confiabilidade a mais de 99%. O racional completo estÃ¡ em
[`docs/13-avaliacao-viabilidade.md`](docs/13-avaliacao-viabilidade.md).

ConsequÃªncias prÃ¡ticas, codificadas como regras inviolÃ¡veis (ver [`CLAUDE.md`](CLAUDE.md)):

1. **Toda aritmÃ©tica vai para Python via MCP** â€” o LLM nunca soma, multiplica ou interpola.
2. **Unidades sÃ£o sempre verificadas com `pint`** antes de qualquer operaÃ§Ã£o.
3. **Todo cÃ¡lculo passa pela camada de validaÃ§Ã£o** antes de ser apresentado.
4. **AbstenÃ§Ã£o:** se nÃ£o existe ferramenta para o pedido, o agente **diz que nÃ£o pode** â€”
   nunca inventa um nÃºmero.
5. **Todo resultado carrega o aviso de responsabilidade tÃ©cnica.**

## Por que existe (escopo e limites)

Esta plataforma Ã© um **copiloto de apoio Ã  engenharia â€” voltado a educaÃ§Ã£o, pesquisa e
verificaÃ§Ã£o cruzada.** Ela **nÃ£o substitui**:

- o software de cÃ¡lculo consagrado (TQS, Eberick/AltoQi, SAP2000, Robot);
- a responsabilidade tÃ©cnica do engenheiro, formalizada via **ART**.

Onde ela agrega valor real:

- **Ensino e prototipagem** â€” interface em linguagem natural + memÃ³ria de cÃ¡lculo explicada.
- **Camada anti-erro-humano** â€” checagem de unidades, seleÃ§Ã£o de fÃ³rmula e consulta a normas.
- **VerificaÃ§Ã£o cruzada** de resultados de outro software.

O posicionamento honesto e suas razÃµes (mercado, academia, profissÃ£o no Brasil) estÃ£o em
[`docs/13-avaliacao-viabilidade.md`](docs/13-avaliacao-viabilidade.md).

## Arquitetura

Quatro camadas, com responsabilidades separadas:

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  USUÃRIO / ENGENHEIRO  â€” linguagem natural                    â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                            â”‚
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  AGENTES (Claude Code / SKILL.md)                             â”‚
â”‚  orchestrator â†’ especialista (concrete, foundations, steelâ€¦)  â”‚
â”‚  interpreta Â· escolhe mÃ©todo Â· explica Â· NUNCA calcula        â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                            â”‚  chama ferramenta MCP
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  MCP SERVER  (mcp/calc-server/server.py)                      â”‚
â”‚  ferramentas finas â†’ orquestradores solve_* (lib/service.py)  â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                            â”‚
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚  ENGINE PYTHON (determinÃ­stica)                               â”‚
â”‚  cÃ¡lculo (lib/<domÃ­nio>) â†’ VALIDAÃ‡ÃƒO (lib/validators)         â”‚
â”‚                          â†’ MEMORIAL (lib/reporting)           â”‚
â”‚  unidades pint Â· modelos Pydantic Â· aviso de responsabilidade â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

**Fluxo de um cÃ¡lculo:** o agente interpreta o enunciado â†’ chama a ferramenta MCP â†’
`solve_*` roda o cÃ¡lculo no nÃºcleo Python â†’ a validaÃ§Ã£o obrigatÃ³ria verifica unidades,
fÃ­sica, norma e equilÃ­brio â†’ o memorial Ã© gerado com o disclaimer â†’ o agente explica cada
etapa e sÃ³ apresenta como soluÃ§Ã£o final se a validaÃ§Ã£o aprovou.

Detalhes em [`docs/01-arquitetura.md`](docs/01-arquitetura.md).

## DomÃ­nios e cÃ¡lculos

18 cÃ¡lculos, cada um com um orquestrador `solve_*` (a fronteira testÃ¡vel em
`lib/service.py`) e uma ferramenta MCP correspondente. Resumo por domÃ­nio:

| DomÃ­nio | CÃ¡lculos | Normas / mÃ©todos |
|---|---|---|
| **Concreto armado** | viga (flexÃ£o), pilar (2Âª ordem), sapata isolada, laje 1 e 2 direÃ§Ãµes | NBR 6118:2014, NBR 6122 |
| **Geotecnia / fundaÃ§Ãµes** | capacidade de carga, recalque (elÃ¡stico/adensamento), empuxo (Rankine/Coulomb), estaca por SPT | Vesic, Terzaghi, Aoki-Velloso Ã— DÃ©court-Quaresma |
| **HidrÃ¡ulica / saneamento** | conduto forÃ§ado, canal aberto, perda de carga | Hazen-Williams, Darcy-Weisbach, Manning |
| **Estruturas metÃ¡licas** | barra comprimida, ligaÃ§Ã£o parafusada, solda de filete | NBR 8800:2008 |
| **PavimentaÃ§Ã£o / terra / taludes** | pavimento flexÃ­vel, balanÃ§o corte/aterro, talude (infinito/Fellenius) | DNER/DNIT, NBR 11682 |
| **OrÃ§amento** | custo direto + BDI | base tipo SINAPI (amostra) |
| **Suporte** | cargas, combinaÃ§Ãµes, consulta/interpolaÃ§Ã£o de norma, memÃ³ria de projeto, relatÃ³rio | NBR 6120, NBR 8681 |

âž¡ï¸ **Lista exaustiva** (com cada `solve_*`, ferramenta MCP e norma): consulte o
[**CatÃ¡logo de CÃ¡lculos** â€” `docs/14-catalogo-calculos.md`](docs/14-catalogo-calculos.md).
A fonte de verdade que o agente consulta em tempo de execuÃ§Ã£o Ã© a ferramenta MCP
`listar_capacidades`.

## InÃ­cio rÃ¡pido

PrÃ©-requisito: [`uv`](https://docs.astral.sh/uv/) (gerenciador de pacotes Python).

```bash
# 1. Instalar dependÃªncias (inclui as de desenvolvimento: pytest, ruff)
uv sync --extra dev

# 2. Rodar a suÃ­te de testes
uv run pytest -q                       # 359 testes

# 3. Conferir a engine ponta a ponta (sem subir o servidor)
uv run python mcp/calc-server/server.py --selftest   # -> SELFTEST OK

# 4. Iniciar o MCP server de cÃ¡lculos (stdio)
uv run python mcp/calc-server/server.py

# 5. Abrir o Claude Code neste diretÃ³rio
claude
```

## Como usar

### a) Via Claude Code (linguagem natural)

Com o MCP server conectado, peÃ§a em portuguÃªs:

> *"Dimensione uma viga 25Ã—50 cm de concreto C30, vÃ£o 5 m, carga permanente 10 kN/m e
> variÃ¡vel 10 kN/m."*

O agente `orchestrator` roteia para o agente `concrete`, que chama a ferramenta MCP
`dimensionar_viga_retangular`, e devolve o memorial completo (esforÃ§os, x/d, armadura,
detalhamento), a validaÃ§Ã£o e o aviso de responsabilidade.

### b) Via Python (a engine direto)

Todo cÃ¡lculo Ã© uma funÃ§Ã£o `solve_*` em `lib/service.py` que devolve o **bundle padrÃ£o**:

```python
from lib.service import solve_rectangular_beam

bundle = solve_rectangular_beam(
    b_cm=25, h_cm=50, fck_mpa=30, fyk_mpa=500,
    vao_m=5.0, g_knm=10, q_knm=10,
)

bundle["aprovado"]               # True  (== bundle["validacao"]["passed"])
bundle["resultado"]["as_adopted_cm2"]   # Ã¡rea de aÃ§o adotada
print(bundle["memorial_markdown"])      # memorial completo, em Markdown
```

O **contrato comum** de todo `solve_*`:

```python
{
  "resultado":         { ... },        # model_dump() do resultado Pydantic
  "validacao":         { "passed": bool, "checks": [...], "warnings": [...] },
  "memorial_markdown": "# Memorial â€” ... (termina com o aviso de responsabilidade)",
  "aviso":             "âš ï¸ RESPONSABILIDADE TÃ‰CNICA ... Lei 6.496/77 ...",
  "aprovado":          bool,           # == validacao.passed
}
```

Quando `aprovado` Ã© `False`, o resultado **nÃ£o** deve ser apresentado como soluÃ§Ã£o final:
o agente aponta o que reprovou (em `validacao.checks`) e orienta o ajuste.

### c) Exemplos prontos

A pasta [`examples/`](examples/) traz casos resolvidos pela prÃ³pria engine (cada um com
`README.md` do problema + `solution.md` gerado): `viga-simples`, `pilar-esbelto`,
`sapata-isolada`, `muro-arrimo`, `estaca-spt`, `rede-agua`, `pilar-metalico`,
`ligacao-parafusada`, `pavimento-flexivel`, `terraplenagem`, `talude`, `orcamento-viga`.

## Pipeline de validaÃ§Ã£o

**Nenhum resultado Ã© apresentado sem passar pela validaÃ§Ã£o** (`lib/validators/`, sem LLM):

1. **Unidades** â€” consistÃªncia dimensional recomputada com `pint`.
2. **FÃ­sica** â€” faixas razoÃ¡veis (ex.: `fck âˆˆ [10, 100] MPa`, `x/d â‰¤ 0,628`, `Ïƒ_solo` plausÃ­vel).
3. **Normativa** â€” limites prescritos (ex.: `As â‰¥ As,mÃ­n`, `As â‰¤ As,mÃ¡x`, ductilidade, FS).
4. **EquilÃ­brio** â€” somatÃ³rio de forÃ§as/momentos (ex.: `Rcc â‰ˆ Rst` na seÃ§Ã£o).

Cada verificaÃ§Ã£o devolve `ok` / `warning` / `fail`; um Ãºnico `fail` reprova o cÃ¡lculo.
Detalhes em [`docs/08-validacao-calculos.md`](docs/08-validacao-calculos.md).

## Responsabilidade tÃ©cnica

Todo memorial termina com o aviso obrigatÃ³rio:

> âš ï¸ **RESPONSABILIDADE TÃ‰CNICA** â€” Este cÃ¡lculo Ã© uma ferramenta de suporte Ã  decisÃ£o de
> engenharia. A responsabilidade tÃ©cnica e legal Ã© exclusiva do engenheiro responsÃ¡vel
> pelo projeto, formalizada via ART (AnotaÃ§Ã£o de Responsabilidade TÃ©cnica), conforme a
> **Lei 6.496/77**, a **Lei 5.194/66** e a ResoluÃ§Ã£o CONFEA/CREA vigente.

No Brasil a responsabilidade Ã© sempre de um profissional habilitado e nomeado via **ART** â€”
nunca de um software. O disclaimer **nÃ£o transfere nem reduz** essa responsabilidade; sÃ³
explicita o que a lei jÃ¡ determina.

## Estrutura do projeto

```
inemaengenhariacivil/
â”œâ”€â”€ lib/                       # engine de cÃ¡lculo (Python determinÃ­stico)
â”‚   â”œâ”€â”€ units/registry.py      # registry pint compartilhado + verificaÃ§Ã£o dimensional
â”‚   â”œâ”€â”€ concrete/              # beams, columns, slabs, footings
â”‚   â”œâ”€â”€ structural/            # loads (NBR 6120), combinations (NBR 8681)
â”‚   â”œâ”€â”€ geotechnical/          # bearing_capacity, settlement, earth_pressure, piles, slope_stability
â”‚   â”œâ”€â”€ hydraulic/             # pipe_flow, open_channel, head_loss
â”‚   â”œâ”€â”€ steel/                 # profiles, stability, connections (NBR 8800)
â”‚   â”œâ”€â”€ pavement/ Â· earthwork/ # pavimento flexÃ­vel Â· volumes/terraplenagem
â”‚   â”œâ”€â”€ budget/                # base SINAPI (amostra) + montagem com BDI
â”‚   â”œâ”€â”€ projects/store.py      # memÃ³ria por projeto (JSON)
â”‚   â”œâ”€â”€ norms/search.py        # busca lÃ©xica em tabelas de norma
â”‚   â”œâ”€â”€ math/interpolation.py  # interpolaÃ§Ã£o de tabelas (sem extrapolar)
â”‚   â”œâ”€â”€ validators/            # pipeline de validaÃ§Ã£o (units/fÃ­sica/norma/equilÃ­brio)
â”‚   â”œâ”€â”€ reporting/             # disclaimer, memorial, relatÃ³rio, export Excel/PDF
â”‚   â””â”€â”€ service.py             # orquestradores solve_* (fronteira testÃ¡vel)
â”œâ”€â”€ mcp/calc-server/server.py  # MCP server (ferramentas finas) + --selftest
â”œâ”€â”€ agents/                    # SKILL.md por agente (orchestrator + domÃ­nios)
â”œâ”€â”€ normas/                    # NBR 6118/6120/6122/7190/8800 (READMEs + tabelas JSON)
â”œâ”€â”€ data/sinapi_amostra.csv    # base de preÃ§os ILUSTRATIVA
â”œâ”€â”€ docs/                      # arquitetura, validaÃ§Ã£o, fases, catÃ¡logo, viabilidadeâ€¦
â”œâ”€â”€ examples/                  # casos resolvidos pela engine (README + solution.md)
â”œâ”€â”€ templates/ Â· checklists/ Â· workflows/
â””â”€â”€ tests/                     # unit/ (nÃºcleos) + integration/ (E2E por domÃ­nio)
```

## Agentes

Os agentes vivem em `agents/<nome>/SKILL.md` (prompt + frontmatter). O **`orchestrator`** Ã©
o ponto de entrada: lÃª o pedido, identifica o domÃ­nio e roteia para o especialista.
Especialistas: `concrete`, `foundations`, `loads`, `review`, `retaining-walls`,
`water-supply`, `sewage`, `steel`, `norms`, `budget`, `report`, `pavement`, `earthwork`,
`slope-stability`. Todos seguem o mesmo contrato: **interpretar, chamar a ferramenta MCP,
explicar, validar e nunca inventar nÃºmero**. Como criar um agente:
[`docs/10-como-adicionar-agente.md`](docs/10-como-adicionar-agente.md).

## Testes e qualidade

```bash
uv run pytest -q                                  # 359 testes (unit + E2E)
uv run ruff check lib mcp tests                   # lint
uv run python mcp/calc-server/server.py --selftest  # smoke da engine ponta a ponta
```

- **TDD** em todo o cÃ³digo: cada funÃ§Ã£o nasceu de um teste que falhou primeiro, com o
  valor de referÃªncia **calculado Ã  mÃ£o** no comentÃ¡rio e a clÃ¡usula normativa citada.
- **`tests/unit/`** cobre cada nÃºcleo de cÃ¡lculo e validador; **`tests/integration/`** roda
  a pilha completa (`solve_*` â†’ validaÃ§Ã£o â†’ memorial) para um caso aprovado de cada domÃ­nio
  e o ciclo de memÃ³ria de projeto.

## Como contribuir

Adicionar um cÃ¡lculo Ã© uma **fatia vertical**: nÃºcleo Pydantic + validador + memorial +
`solve_*` no service + ferramenta MCP + `SKILL.md` do agente â€” **tudo com TDD**. O passo a
passo estÃ¡ em [`CONTRIBUTING.md`](CONTRIBUTING.md) e
[`docs/11-como-adicionar-skill.md`](docs/11-como-adicionar-skill.md).

## LimitaÃ§Ãµes

A engine usa **mÃ©todos consagrados, porÃ©m simplificados e documentados**. Hoje ficam
**fora de escopo** (o agente se abstÃ©m):

- concreto **protendido**, **madeira** (NBR 7190), **alvenaria estrutural**;
- pilar com `Î» > 90`, armadura dupla de viga, cisalhamento/flecha (ELS) de viga;
- lajes com engaste (sÃ³ apoios simples), flambagem local de aÃ§o (`Q â‰  1`), FLT;
- estabilidade de talude por **Bishop/Morgenstern-Price** (sÃ³ infinito e Fellenius);
- pavimento **rÃ­gido**/mecanÃ­stico; dinÃ¢mica/sÃ­smica.

Os preÃ§os da base SINAPI sÃ£o **amostra ilustrativa** (`data/sinapi_amostra.csv`) e devem
ser substituÃ­dos pela tabela vigente/regional. A busca em normas Ã© **lÃ©xica**, nÃ£o semÃ¢ntica.

## Roadmap (fases)

Todas as fases do plano ([`docs/06-fases-implementacao.md`](docs/06-fases-implementacao.md))
estÃ£o **concluÃ­das**:

| VersÃ£o | Fase | Entrega |
|---|---|---|
| v0.1.0 | 1 â€” FundaÃ§Ã£o | viga de concreto + validaÃ§Ã£o + MCP |
| v0.2.0 | 2 â€” Estrutural | pilares, lajes, sapatas, cargas, combinaÃ§Ãµes, exports |
| v0.3.0 | 3 â€” Geo + Hidro | capacidade/recalque/empuxo/estacas + condutos/canal/perdas |
| v0.4.0 | 4 â€” OrÃ§amento + Docs | SINAPI + BDI, memÃ³ria de projeto, relatÃ³rio |
| v0.5.0 | 5 â€” Normas + MetÃ¡licas | NBR 8800, consulta normativa, interpolaÃ§Ã£o |
| **v1.0.0** | 6 â€” Polimento | pavimento/terra/taludes + E2E + docs completas |

## ReferÃªncias normativas

NBR 6118:2014 (concreto), NBR 6120:2019 (aÃ§Ãµes), NBR 6122 (fundaÃ§Ãµes), NBR 7190 (madeira â€”
estrutura prevista), NBR 8800:2008 (aÃ§o), NBR 8681 (combinaÃ§Ãµes), NBR 11682 (taludes),
DNER/DNIT (pavimentaÃ§Ã£o). As tabelas usadas estÃ£o em `normas/<NORMA>/tables/*.json`.

## LicenÃ§a

Uso interno INEMA Engenharia Civil.

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe