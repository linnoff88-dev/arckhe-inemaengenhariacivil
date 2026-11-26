ï»¿# ARCKHÉ Engenharia Civil ê¢â¬â Plataforma de Agentes de IA

Plataforma de **agentes de IA para cêÂ¡lculos de engenharia civil**, construêÂ­da sobre
Claude Code. O agente conversa em linguagem natural, interpreta o problema, escolhe o
mêÂ©todo normativo e **delega toda a aritmêÂ©tica a uma engine determinêÂ­stica em Python** ê¢â¬â
que valida o resultado e gera o memorial de cêÂ¡lculo com o devido aviso de
responsabilidade têÂ©cnica.

> **Estado:** `v1.0.0` ê¢â¬â Fases 1 a 6 concluêÂ­das êÂ· **359 testes** verdes (334 unitêÂ¡rios +
> 25 E2E) êÂ· `ruff` limpo êÂ· `--selftest` do MCP server OK êÂ· 18 domêÂ­nios de cêÂ¡lculo.

---

## êÂndice

1. [O que êÂ©](#o-que-êÂ©)
2. [PrincêÂ­pio central](#princêÂ­pio-central)
3. [Por que existe (escopo e limites)](#por-que-existe-escopo-e-limites)
4. [Arquitetura](#arquitetura)
5. [DomêÂ­nios e cêÂ¡lculos](#domêÂ­nios-e-cêÂ¡lculos)
6. [InêÂ­cio rêÂ¡pido](#inêÂ­cio-rêÂ¡pido)
7. [Como usar](#como-usar)
8. [Pipeline de validaêÂ§êÂ£o](#pipeline-de-validaêÂ§êÂ£o)
9. [Responsabilidade têÂ©cnica](#responsabilidade-têÂ©cnica)
10. [Estrutura do projeto](#estrutura-do-projeto)
11. [Agentes](#agentes)
12. [Testes e qualidade](#testes-e-qualidade)
13. [Como contribuir](#como-contribuir)
14. [LimitaêÂ§êÂµes](#limitaêÂ§êÂµes)
15. [Roadmap (fases)](#roadmap-fases)
16. [ReferêÂªncias normativas](#referêÂªncias-normativas)
17. [LicenêÂ§a](#licenêÂ§a)

---

## O que êÂ©

Uma plataforma onde **engenheiros conversam com agentes de IA** para resolver cêÂ¡lculos de
engenharia civil ê¢â¬â concreto armado, fundaêÂ§êÂµes, geotecnia, hidrêÂ¡ulica, estruturas
metêÂ¡licas, pavimentaêÂ§êÂ£o, terraplenagem, taludes e orêÂ§amento. Cada cêÂ¡lculo entrega:

- o **resultado estruturado** (dados validados),
- um **relatêÂ³rio de validaêÂ§êÂ£o** automêÂ¡tica (4 verificaêÂ§êÂµes independentes),
- um **memorial de cêÂ¡lculo** em Markdown, passo a passo,
- o **aviso de responsabilidade têÂ©cnica** obrigatêÂ³rio.

A IA cuida da parte que ela faz bem ê¢â¬â **entender o problema, escolher o mêÂ©todo, explicar
e documentar**. O nêÂºmero em si nunca sai do modelo de linguagem: sai do Python.

## PrincêÂ­pio central

> **LLMs nêÂ£o fazem contas. Python faz contas. LLMs orquestram, interpretam e documentam.**

Essa nêÂ£o êÂ© uma preferêÂªncia estêÂ©tica ê¢â¬â êÂ© a decisêÂ£o de arquitetura que torna a plataforma
defensêÂ¡vel. A literatura cientêÂ­fica de ponta mostra que um LLM calculando "de cabeêÂ§a"
êÂ© nêÂ£o-confiêÂ¡vel (confiabilidade pode cair abaixo de 10% em problemas de engenharia
estrutural), enquanto **delegar o cêÂ¡lculo a uma ferramenta determinêÂ­stica** leva a
confiabilidade a mais de 99%. O racional completo estêÂ¡ em
[`docs/13-avaliacao-viabilidade.md`](docs/13-avaliacao-viabilidade.md).

ConsequêÂªncias prêÂ¡ticas, codificadas como regras inviolêÂ¡veis (ver [`CLAUDE.md`](CLAUDE.md)):

1. **Toda aritmêÂ©tica vai para Python via MCP** ê¢â¬â o LLM nunca soma, multiplica ou interpola.
2. **Unidades sêÂ£o sempre verificadas com `pint`** antes de qualquer operaêÂ§êÂ£o.
3. **Todo cêÂ¡lculo passa pela camada de validaêÂ§êÂ£o** antes de ser apresentado.
4. **AbstenêÂ§êÂ£o:** se nêÂ£o existe ferramenta para o pedido, o agente **diz que nêÂ£o pode** ê¢â¬â
   nunca inventa um nêÂºmero.
5. **Todo resultado carrega o aviso de responsabilidade têÂ©cnica.**

## Por que existe (escopo e limites)

Esta plataforma êÂ© um **copiloto de apoio êÂ  engenharia ê¢â¬â voltado a educaêÂ§êÂ£o, pesquisa e
verificaêÂ§êÂ£o cruzada.** Ela **nêÂ£o substitui**:

- o software de cêÂ¡lculo consagrado (TQS, Eberick/AltoQi, SAP2000, Robot);
- a responsabilidade têÂ©cnica do engenheiro, formalizada via **ART**.

Onde ela agrega valor real:

- **Ensino e prototipagem** ê¢â¬â interface em linguagem natural + memêÂ³ria de cêÂ¡lculo explicada.
- **Camada anti-erro-humano** ê¢â¬â checagem de unidades, seleêÂ§êÂ£o de fêÂ³rmula e consulta a normas.
- **VerificaêÂ§êÂ£o cruzada** de resultados de outro software.

O posicionamento honesto e suas razêÂµes (mercado, academia, profissêÂ£o no Brasil) estêÂ£o em
[`docs/13-avaliacao-viabilidade.md`](docs/13-avaliacao-viabilidade.md).

## Arquitetura

Quatro camadas, com responsabilidades separadas:

```
ê¢âÅê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ
ê¢ââ  USUêÂRIO / ENGENHEIRO  ê¢â¬â linguagem natural                    ê¢ââ
ê¢ââê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âË
                            ê¢ââ
ê¢âÅê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ¼ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ
ê¢ââ  AGENTES (Claude Code / SKILL.md)                             ê¢ââ
ê¢ââ  orchestrator ê¢â â especialista (concrete, foundations, steelê¢â¬Â¦)  ê¢ââ
ê¢ââ  interpreta êÂ· escolhe mêÂ©todo êÂ· explica êÂ· NUNCA calcula        ê¢ââ
ê¢ââê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âË
                            ê¢ââ  chama ferramenta MCP
ê¢âÅê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ¼ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ
ê¢ââ  MCP SERVER  (mcp/calc-server/server.py)                      ê¢ââ
ê¢ââ  ferramentas finas ê¢â â orquestradores solve_* (lib/service.py)  ê¢ââ
ê¢ââê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âË
                            ê¢ââ
ê¢âÅê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ¼ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âÂ
ê¢ââ  ENGINE PYTHON (determinêÂ­stica)                               ê¢ââ
ê¢ââ  cêÂ¡lculo (lib/<domêÂ­nio>) ê¢â â VALIDAêâ¡êÆO (lib/validators)         ê¢ââ
ê¢ââ                          ê¢â â MEMORIAL (lib/reporting)           ê¢ââ
ê¢ââ  unidades pint êÂ· modelos Pydantic êÂ· aviso de responsabilidade ê¢ââ
ê¢ââê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢ââ¬ê¢âË
```

**Fluxo de um cêÂ¡lculo:** o agente interpreta o enunciado ê¢â â chama a ferramenta MCP ê¢â â
`solve_*` roda o cêÂ¡lculo no nêÂºcleo Python ê¢â â a validaêÂ§êÂ£o obrigatêÂ³ria verifica unidades,
fêÂ­sica, norma e equilêÂ­brio ê¢â â o memorial êÂ© gerado com o disclaimer ê¢â â o agente explica cada
etapa e sêÂ³ apresenta como soluêÂ§êÂ£o final se a validaêÂ§êÂ£o aprovou.

Detalhes em [`docs/01-arquitetura.md`](docs/01-arquitetura.md).

## DomêÂ­nios e cêÂ¡lculos

18 cêÂ¡lculos, cada um com um orquestrador `solve_*` (a fronteira testêÂ¡vel em
`lib/service.py`) e uma ferramenta MCP correspondente. Resumo por domêÂ­nio:

| DomêÂ­nio | CêÂ¡lculos | Normas / mêÂ©todos |
|---|---|---|
| **Concreto armado** | viga (flexêÂ£o), pilar (2êÂª ordem), sapata isolada, laje 1 e 2 direêÂ§êÂµes | NBR 6118:2014, NBR 6122 |
| **Geotecnia / fundaêÂ§êÂµes** | capacidade de carga, recalque (elêÂ¡stico/adensamento), empuxo (Rankine/Coulomb), estaca por SPT | Vesic, Terzaghi, Aoki-Velloso êâ DêÂ©court-Quaresma |
| **HidrêÂ¡ulica / saneamento** | conduto forêÂ§ado, canal aberto, perda de carga | Hazen-Williams, Darcy-Weisbach, Manning |
| **Estruturas metêÂ¡licas** | barra comprimida, ligaêÂ§êÂ£o parafusada, solda de filete | NBR 8800:2008 |
| **PavimentaêÂ§êÂ£o / terra / taludes** | pavimento flexêÂ­vel, balanêÂ§o corte/aterro, talude (infinito/Fellenius) | DNER/DNIT, NBR 11682 |
| **OrêÂ§amento** | custo direto + BDI | base tipo SINAPI (amostra) |
| **Suporte** | cargas, combinaêÂ§êÂµes, consulta/interpolaêÂ§êÂ£o de norma, memêÂ³ria de projeto, relatêÂ³rio | NBR 6120, NBR 8681 |

ê¢Å¾Â¡ê¯Â¸Â **Lista exaustiva** (com cada `solve_*`, ferramenta MCP e norma): consulte o
[**CatêÂ¡logo de CêÂ¡lculos** ê¢â¬â `docs/14-catalogo-calculos.md`](docs/14-catalogo-calculos.md).
A fonte de verdade que o agente consulta em tempo de execuêÂ§êÂ£o êÂ© a ferramenta MCP
`listar_capacidades`.

## InêÂ­cio rêÂ¡pido

PrêÂ©-requisito: [`uv`](https://docs.astral.sh/uv/) (gerenciador de pacotes Python).

```bash
# 1. Instalar dependêÂªncias (inclui as de desenvolvimento: pytest, ruff)
uv sync --extra dev

# 2. Rodar a suêÂ­te de testes
uv run pytest -q                       # 359 testes

# 3. Conferir a engine ponta a ponta (sem subir o servidor)
uv run python mcp/calc-server/server.py --selftest   # -> SELFTEST OK

# 4. Iniciar o MCP server de cêÂ¡lculos (stdio)
uv run python mcp/calc-server/server.py

# 5. Abrir o Claude Code neste diretêÂ³rio
claude
```

## Como usar

### a) Via Claude Code (linguagem natural)

Com o MCP server conectado, peêÂ§a em portuguêÂªs:

> *"Dimensione uma viga 25êâ50 cm de concreto C30, vêÂ£o 5 m, carga permanente 10 kN/m e
> variêÂ¡vel 10 kN/m."*

O agente `orchestrator` roteia para o agente `concrete`, que chama a ferramenta MCP
`dimensionar_viga_retangular`, e devolve o memorial completo (esforêÂ§os, x/d, armadura,
detalhamento), a validaêÂ§êÂ£o e o aviso de responsabilidade.

### b) Via Python (a engine direto)

Todo cêÂ¡lculo êÂ© uma funêÂ§êÂ£o `solve_*` em `lib/service.py` que devolve o **bundle padrêÂ£o**:

```python
from lib.service import solve_rectangular_beam

bundle = solve_rectangular_beam(
    b_cm=25, h_cm=50, fck_mpa=30, fyk_mpa=500,
    vao_m=5.0, g_knm=10, q_knm=10,
)

bundle["aprovado"]               # True  (== bundle["validacao"]["passed"])
bundle["resultado"]["as_adopted_cm2"]   # êÂ¡rea de aêÂ§o adotada
print(bundle["memorial_markdown"])      # memorial completo, em Markdown
```

O **contrato comum** de todo `solve_*`:

```python
{
  "resultado":         { ... },        # model_dump() do resultado Pydantic
  "validacao":         { "passed": bool, "checks": [...], "warnings": [...] },
  "memorial_markdown": "# Memorial ê¢â¬â ... (termina com o aviso de responsabilidade)",
  "aviso":             "ê¢Å¡Â ê¯Â¸Â RESPONSABILIDADE Têâ°CNICA ... Lei 6.496/77 ...",
  "aprovado":          bool,           # == validacao.passed
}
```

Quando `aprovado` êÂ© `False`, o resultado **nêÂ£o** deve ser apresentado como soluêÂ§êÂ£o final:
o agente aponta o que reprovou (em `validacao.checks`) e orienta o ajuste.

### c) Exemplos prontos

A pasta [`examples/`](examples/) traz casos resolvidos pela prêÂ³pria engine (cada um com
`README.md` do problema + `solution.md` gerado): `viga-simples`, `pilar-esbelto`,
`sapata-isolada`, `muro-arrimo`, `estaca-spt`, `rede-agua`, `pilar-metalico`,
`ligacao-parafusada`, `pavimento-flexivel`, `terraplenagem`, `talude`, `orcamento-viga`.

## Pipeline de validaêÂ§êÂ£o

**Nenhum resultado êÂ© apresentado sem passar pela validaêÂ§êÂ£o** (`lib/validators/`, sem LLM):

1. **Unidades** ê¢â¬â consistêÂªncia dimensional recomputada com `pint`.
2. **FêÂ­sica** ê¢â¬â faixas razoêÂ¡veis (ex.: `fck ê¢ËË [10, 100] MPa`, `x/d ê¢â°Â¤ 0,628`, `êÆ_solo` plausêÂ­vel).
3. **Normativa** ê¢â¬â limites prescritos (ex.: `As ê¢â°Â¥ As,mêÂ­n`, `As ê¢â°Â¤ As,mêÂ¡x`, ductilidade, FS).
4. **EquilêÂ­brio** ê¢â¬â somatêÂ³rio de forêÂ§as/momentos (ex.: `Rcc ê¢â°Ë Rst` na seêÂ§êÂ£o).

Cada verificaêÂ§êÂ£o devolve `ok` / `warning` / `fail`; um êÂºnico `fail` reprova o cêÂ¡lculo.
Detalhes em [`docs/08-validacao-calculos.md`](docs/08-validacao-calculos.md).

## Responsabilidade têÂ©cnica

Todo memorial termina com o aviso obrigatêÂ³rio:

> ê¢Å¡Â ê¯Â¸Â **RESPONSABILIDADE Têâ°CNICA** ê¢â¬â Este cêÂ¡lculo êÂ© uma ferramenta de suporte êÂ  decisêÂ£o de
> engenharia. A responsabilidade têÂ©cnica e legal êÂ© exclusiva do engenheiro responsêÂ¡vel
> pelo projeto, formalizada via ART (AnotaêÂ§êÂ£o de Responsabilidade TêÂ©cnica), conforme a
> **Lei 6.496/77**, a **Lei 5.194/66** e a ResoluêÂ§êÂ£o CONFEA/CREA vigente.

No Brasil a responsabilidade êÂ© sempre de um profissional habilitado e nomeado via **ART** ê¢â¬â
nunca de um software. O disclaimer **nêÂ£o transfere nem reduz** essa responsabilidade; sêÂ³
explicita o que a lei jêÂ¡ determina.

## Estrutura do projeto

```
ARCKHÉengenhariacivil/
ê¢âÅê¢ââ¬ê¢ââ¬ lib/                       # engine de cêÂ¡lculo (Python determinêÂ­stico)
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ units/registry.py      # registry pint compartilhado + verificaêÂ§êÂ£o dimensional
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ concrete/              # beams, columns, slabs, footings
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ structural/            # loads (NBR 6120), combinations (NBR 8681)
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ geotechnical/          # bearing_capacity, settlement, earth_pressure, piles, slope_stability
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ hydraulic/             # pipe_flow, open_channel, head_loss
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ steel/                 # profiles, stability, connections (NBR 8800)
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ pavement/ êÂ· earthwork/ # pavimento flexêÂ­vel êÂ· volumes/terraplenagem
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ budget/                # base SINAPI (amostra) + montagem com BDI
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ projects/store.py      # memêÂ³ria por projeto (JSON)
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ norms/search.py        # busca lêÂ©xica em tabelas de norma
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ math/interpolation.py  # interpolaêÂ§êÂ£o de tabelas (sem extrapolar)
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ validators/            # pipeline de validaêÂ§êÂ£o (units/fêÂ­sica/norma/equilêÂ­brio)
ê¢ââ   ê¢âÅê¢ââ¬ê¢ââ¬ reporting/             # disclaimer, memorial, relatêÂ³rio, export Excel/PDF
ê¢ââ   ê¢ââê¢ââ¬ê¢ââ¬ service.py             # orquestradores solve_* (fronteira testêÂ¡vel)
ê¢âÅê¢ââ¬ê¢ââ¬ mcp/calc-server/server.py  # MCP server (ferramentas finas) + --selftest
ê¢âÅê¢ââ¬ê¢ââ¬ agents/                    # SKILL.md por agente (orchestrator + domêÂ­nios)
ê¢âÅê¢ââ¬ê¢ââ¬ normas/                    # NBR 6118/6120/6122/7190/8800 (READMEs + tabelas JSON)
ê¢âÅê¢ââ¬ê¢ââ¬ data/sinapi_amostra.csv    # base de preêÂ§os ILUSTRATIVA
ê¢âÅê¢ââ¬ê¢ââ¬ docs/                      # arquitetura, validaêÂ§êÂ£o, fases, catêÂ¡logo, viabilidadeê¢â¬Â¦
ê¢âÅê¢ââ¬ê¢ââ¬ examples/                  # casos resolvidos pela engine (README + solution.md)
ê¢âÅê¢ââ¬ê¢ââ¬ templates/ êÂ· checklists/ êÂ· workflows/
ê¢ââê¢ââ¬ê¢ââ¬ tests/                     # unit/ (nêÂºcleos) + integration/ (E2E por domêÂ­nio)
```

## Agentes

Os agentes vivem em `agents/<nome>/SKILL.md` (prompt + frontmatter). O **`orchestrator`** êÂ©
o ponto de entrada: lêÂª o pedido, identifica o domêÂ­nio e roteia para o especialista.
Especialistas: `concrete`, `foundations`, `loads`, `review`, `retaining-walls`,
`water-supply`, `sewage`, `steel`, `norms`, `budget`, `report`, `pavement`, `earthwork`,
`slope-stability`. Todos seguem o mesmo contrato: **interpretar, chamar a ferramenta MCP,
explicar, validar e nunca inventar nêÂºmero**. Como criar um agente:
[`docs/10-como-adicionar-agente.md`](docs/10-como-adicionar-agente.md).

## Testes e qualidade

```bash
uv run pytest -q                                  # 359 testes (unit + E2E)
uv run ruff check lib mcp tests                   # lint
uv run python mcp/calc-server/server.py --selftest  # smoke da engine ponta a ponta
```

- **TDD** em todo o cêÂ³digo: cada funêÂ§êÂ£o nasceu de um teste que falhou primeiro, com o
  valor de referêÂªncia **calculado êÂ  mêÂ£o** no comentêÂ¡rio e a clêÂ¡usula normativa citada.
- **`tests/unit/`** cobre cada nêÂºcleo de cêÂ¡lculo e validador; **`tests/integration/`** roda
  a pilha completa (`solve_*` ê¢â â validaêÂ§êÂ£o ê¢â â memorial) para um caso aprovado de cada domêÂ­nio
  e o ciclo de memêÂ³ria de projeto.

## Como contribuir

Adicionar um cêÂ¡lculo êÂ© uma **fatia vertical**: nêÂºcleo Pydantic + validador + memorial +
`solve_*` no service + ferramenta MCP + `SKILL.md` do agente ê¢â¬â **tudo com TDD**. O passo a
passo estêÂ¡ em [`CONTRIBUTING.md`](CONTRIBUTING.md) e
[`docs/11-como-adicionar-skill.md`](docs/11-como-adicionar-skill.md).

## LimitaêÂ§êÂµes

A engine usa **mêÂ©todos consagrados, porêÂ©m simplificados e documentados**. Hoje ficam
**fora de escopo** (o agente se abstêÂ©m):

- concreto **protendido**, **madeira** (NBR 7190), **alvenaria estrutural**;
- pilar com `êÂ» > 90`, armadura dupla de viga, cisalhamento/flecha (ELS) de viga;
- lajes com engaste (sêÂ³ apoios simples), flambagem local de aêÂ§o (`Q ê¢â°Â  1`), FLT;
- estabilidade de talude por **Bishop/Morgenstern-Price** (sêÂ³ infinito e Fellenius);
- pavimento **rêÂ­gido**/mecanêÂ­stico; dinêÂ¢mica/sêÂ­smica.

Os preêÂ§os da base SINAPI sêÂ£o **amostra ilustrativa** (`data/sinapi_amostra.csv`) e devem
ser substituêÂ­dos pela tabela vigente/regional. A busca em normas êÂ© **lêÂ©xica**, nêÂ£o semêÂ¢ntica.

## Roadmap (fases)

Todas as fases do plano ([`docs/06-fases-implementacao.md`](docs/06-fases-implementacao.md))
estêÂ£o **concluêÂ­das**:

| VersêÂ£o | Fase | Entrega |
|---|---|---|
| v0.1.0 | 1 ê¢â¬â FundaêÂ§êÂ£o | viga de concreto + validaêÂ§êÂ£o + MCP |
| v0.2.0 | 2 ê¢â¬â Estrutural | pilares, lajes, sapatas, cargas, combinaêÂ§êÂµes, exports |
| v0.3.0 | 3 ê¢â¬â Geo + Hidro | capacidade/recalque/empuxo/estacas + condutos/canal/perdas |
| v0.4.0 | 4 ê¢â¬â OrêÂ§amento + Docs | SINAPI + BDI, memêÂ³ria de projeto, relatêÂ³rio |
| v0.5.0 | 5 ê¢â¬â Normas + MetêÂ¡licas | NBR 8800, consulta normativa, interpolaêÂ§êÂ£o |
| **v1.0.0** | 6 ê¢â¬â Polimento | pavimento/terra/taludes + E2E + docs completas |

## ReferêÂªncias normativas

NBR 6118:2014 (concreto), NBR 6120:2019 (aêÂ§êÂµes), NBR 6122 (fundaêÂ§êÂµes), NBR 7190 (madeira ê¢â¬â
estrutura prevista), NBR 8800:2008 (aêÂ§o), NBR 8681 (combinaêÂ§êÂµes), NBR 11682 (taludes),
DNER/DNIT (pavimentaêÂ§êÂ£o). As tabelas usadas estêÂ£o em `normas/<NORMA>/tables/*.json`.

## LicenêÂ§a

Uso interno ARCKHÉ Engenharia Civil.

---

Projeto  - ARCKHÉe