# Estrutura de Diretórios

## Árvore Completa

```
ARCKHÉengenhariacivil/
│
├── CLAUDE.md                        # Configuraçêo Claude Code
├── README.md                        # Introduçêo ao projeto
├── pyproject.toml                   # Dependências Python
├── .gitignore
│
├── docs/                            # Documentaçêo (esta pasta)
│   ├── 00-visao-geral.md
│   ├── 01-arquitetura.md
│   ├── 02-estrutura-diretorios.md
│   ├── 03-agentes.md
│   ├── 04-skills.md
│   ├── 05-dependencias.md
│   ├── 06-fases-implementacao.md
│   ├── 07-estrategia-testes.md
│   ├── 08-validacao-calculos.md
│   ├── 09-evolucao-futura.md
│   ├── 10-como-adicionar-agente.md
│   ├── 11-como-adicionar-skill.md
│   └── 12-como-adicionar-norma.md
│
├── agents/                          # Claude Code Skills (SKILL.md por agente)
│   ├── orchestrator/
│   │   └── SKILL.md
│   ├── structural/
│   │   ├── concrete/
│   │   │   └── SKILL.md
│   │   ├── steel/
│   │   │   └── SKILL.md
│   │   ├── timber/
│   │   │   └── SKILL.md
│   │   └── masonry/
│   │       └── SKILL.md
│   ├── geotechnical/
│   │   ├── foundations/
│   │   │   └── SKILL.md
│   │   ├── retaining-walls/
│   │   │   └── SKILL.md
│   │   └── slope-stability/
│   │       └── SKILL.md
│   ├── hydraulic/
│   │   ├── water-supply/
│   │   │   └── SKILL.md
│   │   ├── sewage/
│   │   │   └── SKILL.md
│   │   └── stormwater/
│   │       └── SKILL.md
│   ├── roads/
│   │   ├── pavement/
│   │   │   └── SKILL.md
│   │   └── earthwork/
│   │       └── SKILL.md
│   ├── budget/
│   │   └── SKILL.md
│   ├── review/
│   │   └── SKILL.md
│   ├── report/
│   │   └── SKILL.md
│   └── norms/
│       └── SKILL.md
│
├── lib/                             # Engine de cálculo Python
│   ├── __init__.py
│   ├── concrete/
│   │   ├── __init__.py
│   │   ├── beams.py
│   │   ├── columns.py
│   │   ├── slabs.py
│   │   ├── footings.py
│   │   └── materials.py
│   ├── steel/
│   │   ├── __init__.py
│   │   ├── profiles.py
│   │   ├── connections.py
│   │   └── stability.py
│   ├── geotechnical/
│   │   ├── __init__.py
│   │   ├── bearing_capacity.py
│   │   ├── settlement.py
│   │   ├── earth_pressure.py
│   │   └── piles.py
│   ├── hydraulic/
│   │   ├── __init__.py
│   │   ├── pipe_flow.py
│   │   ├── open_channel.py
│   │   └── head_loss.py
│   ├── structural/
│   │   ├── __init__.py
│   │   ├── loads.py
│   │   ├── combinations.py
│   │   └── fem_simple.py
│   ├── units/
│   │   ├── __init__.py
│   │   └── converter.py
│   ├── math/
│   │   ├── __init__.py
│   │   ├── linear_algebra.py
│   │   ├── numerical.py
│   │   └── interpolation.py
│   ├── validators/
│   │   ├── __init__.py
│   │   ├── units_check.py
│   │   ├── physical_check.py
│   │   └── normative_check.py
│   └── reporting/
│       ├── __init__.py
│       ├── memorial.py
│       ├── excel_export.py
│       └── pdf_export.py
│
├── mcp/                             # MCP Servers (bridge LLM ↔ Python)
│   ├── calc-server/
│   │   ├── server.py
│   │   └── tools.py
│   └── report-server/
│       └── server.py
│
├── workflows/                       # Workflows reutilizáveis
│   ├── concrete-beam.md
│   ├── pile-foundation.md
│   ├── water-network.md
│   └── budget-estimate.md
│
├── normas/                          # Referências normativas
│   ├── NBR6118/
│   │   ├── tables/
│   │   └── README.md
│   ├── NBR6120/
│   │   ├── tables/
│   │   └── README.md
│   ├── NBR6122/
│   │   ├── tables/
│   │   └── README.md
│   ├── NBR8800/
│   │   ├── tables/
│   │   └── README.md
│   └── NBR7190/
│       ├── tables/
│       └── README.md
│
├── templates/                       # Templates de documentos
│   ├── memorial-calculo.md
│   ├── relatorio-tecnico.md
│   ├── checklist-revisao.md
│   └── laudo-pericial.md
│
├── checklists/                      # Checklists por tipo de cálculo
│   ├── viga-concreto.json
│   ├── sapata.json
│   └── rede-hidraulica.json
│
├── memory/                          # Memória persistente
│   ├── projects/
│   │   └── .gitkeep
│   └── preferences.yaml
│
├── examples/                        # Exemplos completos resolvidos
│   ├── viga-simples/
│   │   └── README.md
│   ├── pilar-esbelto/
│   │   └── README.md
│   └── sapata-isolada/
│       └── README.md
│
└── tests/                           # Testes automatizados
    ├── unit/
    │   └── .gitkeep
    ├── integration/
    │   └── .gitkeep
    └── fixtures/
        └── .gitkeep
```

## Convenções de Nomenclatura

| Componente | Convençêo | Exemplo |
|------------|-----------|---------|
| Agentes | kebab-case | `concrete-beam` |
| Módulos Python | snake_case | `bearing_capacity.py` |
| Classes Python | PascalCase | `BeamDesignInput` |
| Funções Python | snake_case | `design_t_beam()` |
| Variáveis | snake_case com unidade | `fck_mpa`, `vao_m` |
| Constantes | UPPER_SNAKE | `GAMMA_C = 1.4` |
