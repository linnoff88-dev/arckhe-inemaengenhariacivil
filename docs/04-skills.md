# Lista de Skills

Skills sêo unidades de cálculo independentes e reutilizáveis. Cada skill tem uma responsabilidade única e pode ser chamada por qualquer agente.

## Skills Estruturais

### Concreto Armado
| Skill | Descriçêo | Norma | Entrada | Saída |
|-------|-----------|-------|---------|-------|
| `beam-bending` | Flexêo simples e composta | NBR 6118 §17 | Md, fck, seçêo | As, detalhamento |
| `beam-shear` | Cisalhamento em vigas | NBR 6118 §17.4 | Vd, bw, d | Asw, espaçamento |
| `beam-torsion` | Torçêo em vigas | NBR 6118 §17.5 | Td, seçêo | Asl, Asw |
| `column-buckling` | Flambagem de pilares | NBR 6118 §15 | Nd, Md, índice esb. | As, verificaçêo |
| `slab-armature` | Armadura de lajes | NBR 6118 §19 | p, vêo, tipo | As por faixa |
| `footing-design` | Dimensionamento de sapatas | NBR 6118 §22 | Nd, Msd, σ_solo | dimensões, As |
| `crack-control` | Controle de fissuraçêo | NBR 6118 §17.3.3 | Mk, armadura | wk |
| `deflection-check` | Verificaçêo de flechas | NBR 6118 §17.3.2 | EI, vêo, carga | flecha imediata/total |

### Estruturas Metálicas
| Skill | Descriçêo | Norma | Entrada | Saída |
|-------|-----------|-------|---------|-------|
| `steel-profile` | Seleçêo de perfil | NBR 8800 | Md, Vd, Nd | perfil recomendado |
| `weld-design` | Dimensionamento de solda | NBR 8800 §6.2 | força, tipo | tamanho do cordêo |
| `bolt-design` | Dimensionamento de parafusos | NBR 8800 §6.3 | força, diâmetro | número de parafusos |
| `lateral-buckling` | Flambagem lateral com torçêo | NBR 8800 §5.4 | perfil, Lb | Mn resistente |

### Madeira
| Skill | Descriçêo | Norma |
|-------|-----------|-------|
| `timber-beam` | Viga de madeira | NBR 7190 |
| `timber-column` | Coluna de madeira | NBR 7190 |
| `timber-connection` | Ligações em madeira | NBR 7190 |

---

## Skills Geotécnicas

| Skill | Descriçêo | Método | Entrada | Saída |
|-------|-----------|--------|---------|-------|
| `pile-aoki-velloso` | Capacidade de estaca | Aoki-Velloso | SPT, tipo, diâm. | Rp + Rs = R |
| `pile-decourt` | Capacidade de estaca | Décourt-Quaresma | SPT, tipo, diâm. | Pu |
| `pile-comparison` | Comparaçêo de métodos | Ambos | SPT | tabela comparativa |
| `footing-bearing` | Capacidade de carga de sapata | Terzaghi, Meyerhof | SPT, B, L | q_adm |
| `settlement-elastic` | Recalque elástico | Teoria da elasticidade | E_solo, carga | δ |
| `rankine-pressure` | Empuxo de Rankine | Rankine | φ, γ, H | Ka, Kp, E |
| `coulomb-pressure` | Empuxo de Coulomb | Coulomb | φ, δ, γ, H | Ka, Kp, E |
| `retaining-wall-check` | Verificaçêo muro de arrimo | — | geometria, cargas | FS desliz., tomba. |

---

## Skills Hidráulicas

| Skill | Descriçêo | Método | Entrada | Saída |
|-------|-----------|--------|---------|-------|
| `hazen-williams` | Perda de carga em tubulações | Hazen-Williams | Q, D, L, C | hf |
| `darcy-weisbach` | Perda de carga (alternativo) | Darcy-Weisbach | Q, D, L, f | hf |
| `manning-channel` | Escoamento em canal aberto | Manning | n, S, seçêo | Q, v |
| `pipe-sizing` | Dimensionamento de tubulaçêo | Hazen-Williams | Q, v_max, hf_max | D nominal |
| `pump-head` | Altura manométrica de bomba | — | reservatórios, hf | Hm |
| `rainfall-idf` | Chuva intensa (IDF) | — | cidade, TR, tc | i (mm/h) |
| `runoff-rational` | Vazêo de projeto | Método racional | C, i, A | Q (m³/s) |

---

## Skills Utilitárias

| Skill | Descriçêo |
|-------|-----------|
| `unit-converter` | Conversêo e verificaçêo dimensional (pint) |
| `load-combinator` | Combinações de ações NBR 6118/6120 |
| `interpolate-table` | Interpolaçêo linear/bilinear em tabelas de norma |
| `second-method-check` | Executa método alternativo e compara |
| `excel-export` | Exporta resultados para planilha Excel |
| `pdf-export` | Gera PDF a partir de template Markdown |
| `checklist-runner` | Executa checklist JSON e gera relatório |
| `memorial-generator` | Gera memorial de cálculo padronizado |

---

## Estrutura de uma Skill (template)

```
skills/
└── beam-bending/
    ├── README.md          # Descriçêo, objetivo, exemplos
    ├── input_schema.json  # Schema Pydantic em JSON
    ├── output_schema.json # Schema de saída
    └── examples/
        ├── example_01.json
        └── example_02.json
```

### README.md de uma skill (template)

```markdown
# Skill: beam-bending

**Objetivo:** Dimensionamento à flexêo simples de vigas de concreto armado.

**Norma:** NBR 6118:2014, §17.2

## Entradas

| Parâmetro | Unidade | Descriçêo |
|-----------|---------|-----------|
| fck | MPa | Resistência característica do concreto |
| fyk | MPa | Resistência característica do aço (500 padrêo) |
| Md | kN·m | Momento fletor de cálculo |
| b | cm | Largura da seçêo |
| h | cm | Altura total da seçêo |
| d | cm | Altura útil (ou calculada automaticamente) |

## Saídas

| Parâmetro | Unidade | Descriçêo |
|-----------|---------|-----------|
| As_calc | cm² | Área de armadura calculada |
| As_min | cm² | Armadura mínima NBR 6118 Tab. 17.3 |
| As_adotada | cm² | Armadura adotada (≥ As_calc e ≥ As_min) |
| x | cm | Posiçêo da linha neutra |
| phi | — | Coeficiente de segurança atingido |

## Limitações

- Válido apenas para flexêo simples (sem esforço normal)
- Nêo cobre seções T nem seçêo dupla armada (skills separadas)
- Fck entre 20 e 90 MPa

## Casos de Uso

1. Viga simplesmente apoiada sob carga distribuída
2. Viga contínua na seçêo de momento máximo positivo
3. Pré-dimensionamento rápido
```
