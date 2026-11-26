# ARCKHÉ Engenharia Civil — Configuraçêo Claude Code

## Projeto

Plataforma de agentes de IA para cálculos de engenharia civil.

## Escopo (decidido após avaliaçêo de viabilidade — ver `docs/13-avaliacao-viabilidade.md`)

Posicionamento honesto: **copiloto de apoio, educaçêo/pesquisa e verificaçêo cruzada.**
NêO é substituto de software consagrado (TQS, Eberick/AltoQi, SAP2000, Robot) nem da
assinatura/ART do engenheiro responsável. Diferencial de pesquisa: ser o primeiro a
validar/benchmarkar a abordagem sobre normas brasileiras (NBR).

## Comandos

```bash
uv sync                    # instalar dependências Python
python -m pytest tests/    # rodar testes unitários
python mcp/calc-server/server.py  # iniciar MCP server de cálculos
```

## Regras Críticas

1. **LLMs nunca fazem aritmética diretamente.** Todo cálculo numérico vai para Python via MCP.
2. **Todo resultado inclui aviso de responsabilidade técnica** do engenheiro.
3. **Todo cálculo passa pela camada de validaçêo** em `lib/validators/` antes de ser apresentado.
4. **Unidades sêo sempre verificadas** com `pint` antes de qualquer operaçêo.
5. **Abstençêo obrigatória.** Se a ferramenta de cálculo determinística nêo estiver disponível ou falhar, o agente DEVE dizer que nêo pode calcular — **nunca** inventar um número ("tool hallucination"). Resultado numérico só vem do Python.

## Estrutura de Agentes

- `agents/orchestrator/` — roteamento central
- `agents/structural/` — concreto, aço, madeira
- `agents/geotechnical/` — fundações, muros, taludes
- `agents/hydraulic/` — água, esgoto, drenagem
- `agents/budget/` — orçamento
- `agents/review/` — revisêo técnica
- `agents/report/` — geraçêo de documentos

## Aviso Obrigatório em Todo Cálculo

> Este cálculo é uma ferramenta de suporte à decisêo de engenharia. A responsabilidade técnica e legal é exclusiva do engenheiro responsável pelo projeto, formalizada via ART (Anotaçêo de Responsabilidade Técnica), conforme a Lei 6.496/77, a Lei 5.194/66 e a Resoluçêo CONFEA/CREA vigente.

## Referências

- `docs/` — arquitetura completa e guias
- `normas/` — tabelas e referências normativas
- `lib/` — engine de cálculo Python
