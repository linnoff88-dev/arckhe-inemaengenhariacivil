# NBR 6118:2014 — Projeto de Estruturas de Concreto

## Metadados

- **Código:** ABNT NBR 6118
- **Ediçêo:** 2014 (3ª ediçêo)
- **Título:** Projeto de estruturas de concreto — Procedimento
- **Status:** Em vigor
- **Substitui:** NBR 6118:2003

## Tabelas Disponíveis

| Arquivo | Tabela NBR | Conteúdo |
|---------|-----------|---------|
| `tabela_6_1.json` | Tab. 6.1 | Classes de agressividade ambiental |
| `tabela_7_2.json` | Tab. 7.2 | Correspondência entre classe de agressividade e cobrimento |
| `tabela_17_3.json` | Tab. 17.3 | Taxas de armadura mínima de vigas |
| `tabela_19_1.json` | Tab. 19.1 | Relações máximas vêo/espessura de lajes |

## Seções Mais Utilizadas

| Seçêo | Assunto |
|-------|---------|
| §6 | Durabilidade |
| §7 | Agressividade e cobrimento |
| §11 | Resistência do concreto |
| §15 | Instabilidade e efeitos de 2ª ordem |
| §17 | Dimensionamento: flexêo, cisalhamento, torçêo |
| §18 | Punçêo |
| §19 | Lajes |
| §22 | Elementos de fundaçêo |

## Uso no Código

```python
from lib.validators.normative_check import get_norm_table, check_coverage

tabela = get_norm_table("NBR6118", "7.2")

check_coverage(
    cobrimento_mm=30,
    classe_agressividade="CA-II",
    norma="NBR6118"
)
```
