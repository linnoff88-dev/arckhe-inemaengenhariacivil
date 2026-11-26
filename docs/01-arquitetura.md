# Arquitetura da Plataforma

## Diagrama de Camadas

```
┌─────────────────────────────────────────────────────────┐
│                    USUÁRIO / ENGENHEIRO                  │
└─────────────────────────┬───────────────────────────────┘
                          │  linguagem natural
┌─────────────────────────▼───────────────────────────────┐
│              CAMADA DE AGENTES (Claude Code Skills)      │
│                                                          │
│  ┌──────────────┐   ┌──────────────┐                    │
│  │ orchestrator │──►│  especialista│                    │
│  └──────────────┘   └──────┬───────┘                    │
│                            │ interpreta + seleciona      │
└────────────────────────────┼────────────────────────────┘
                             │ chama ferramenta MCP
┌────────────────────────────▼────────────────────────────┐
│                   CAMADA MCP (Bridge)                    │
│                                                          │
│  ┌──────────────┐   ┌──────────────┐   ┌────────────┐  │
│  │  calc-server │   │ report-server│   │  fs-server │  │
│  └──────┬───────┘   └──────┬───────┘   └────────────┘  │
└─────────┼──────────────────┼─────────────────────────── ┘
          │ chama funçêo     │ gera documento
┌─────────▼──────────────────▼────────────────────────────┐
│              ENGINE DE CÁLCULO (Python)                  │
│                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │
│  │ concrete │  │  steel   │  │   geo    │  │ hydro  │  │
│  └──────────┘  └──────────┘  └──────────┘  └────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │              VALIDATOR (obrigatório)             │   │
│  │  units • physical • normative • equilibrium      │   │
│  └──────────────────────────────────────────────────┘   │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │              REPORTER                            │   │
│  │  memorial • excel • pdf • checklist              │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

## Fluxo Detalhado de um Cálculo

```
1. ENTRADA
   Usuário: "Dimensione uma viga T de concreto C30, vêo 6m, carga 30kN/m"

2. ORQUESTRADOR
   → identifica domínio: concreto estrutural
   → roteia para agente: concrete

3. AGENTE CONCRETE
   → interpreta: viga de concreto armado, flexêo simples
   → identifica norma: NBR 6118:2014
   → seleciona método: seçêo T, estado limite último
   → prepara parâmetros de entrada

4. MCP CALL → calc-server
   → ferramenta: calculate_beam_flexure
   → entrada: {fck: 30, vao: 6.0, carga: 30, tipo: "T"}

5. PYTHON ENGINE
   → lib/concrete/beams.py::design_t_beam()
   → calcula Md, escolhe armadura, verifica deflexêo

6. VALIDATOR (automático)
   → check_units: kN·m ✓
   → check_physical: As > 0 ✓
   → check_normative: As_min < As < As_max ✓
   → check_equilibrium: equilíbrio de forças ✓

7. RESULTADO ESTRUTURADO
   → As_calculada: 8.5 cm²
   → escolha: 4 φ 16mm (As = 8.04 cm²) — verifica com ρ_min
   → detalhamento: cobrimento 3cm, estribos φ8c/20cm

8. AGENTE CONCRETE (continuaçêo)
   → explica cada etapa do cálculo
   → mostra hipóteses adotadas
   → identifica limitações

9. REPORTER
   → gera memorial de cálculo (Markdown → PDF)
   → gera planilha de resultados (Excel)
   → gera checklist de revisêo

10. SAÍDA
    → Memorial PDF + Excel + Checklist
    → Aviso de responsabilidade técnica obrigatório
```

## Princípios de Design

### Separaçêo de responsabilidades
- Agentes: interpretaçêo, seleçêo de método, explicaçêo
- Python: toda aritmética e lógica de cálculo
- MCP: protocolo de comunicaçêo entre as camadas
- Validator: verificações determinísticas, sem LLM

### Contrato de dados (Pydantic)
Todo input e output de funçêo Python tem schema Pydantic:
- Garante que unidades estêo presentes
- Impede parâmetros ausentes
- Gera documentaçêo automática

### Segundo método de verificaçêo
Para cálculos críticos, dois métodos independentes sêo executados.
Se divergência > 20%, o agente avisa o engenheiro para revisêo manual.
