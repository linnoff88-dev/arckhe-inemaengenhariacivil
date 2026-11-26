> **Nota de escopo (gerado pela engine `lib.service.solve_earthwork`).**
> Balanço corte × aterro (volumes geométricos), com volume solto por empolamento e os
> volumes de empréstimo/bota-fora. Diagrama de massas (Brückner), DMT, equipamentos,
> custo e compactaçêo no campo estêo fora do escopo — princípio da abstençêo.

# Memorial de Cálculo — Terraplenagem (Balanço Corte/Aterro)

**Método:** Balanço corte/aterro (áreas médias + empolamento)  
**Referência:** DNIT, Manual de Implantaçêo Básica de Rodovia

## 1. Dados de entrada

| Parâmetro | Valor |
|-----------|-------|
| Volume de corte (banco) | 5000.0 m³ |
| Volume de aterro (compactado) | 3000.0 m³ |
| Fator de empolamento Fe | 1.25 |

## 2. Balanço de volumes

- Volume de corte solto (transporte): V_solto = corte·Fe = **6250.0 m³**
- **Balanço = corte − aterro = 2000.0 m³** (situaçêo: **excesso**)
- Volume de empréstimo (déficit): 0.0 m³
- Volume de bota-fora (excesso): 2000.0 m³ (solto: 2500.0 m³)

## 3. Validaçêo automática

| Verificaçêo | Status | Detalhe |
|-------------|--------|---------|
| units | ✓ | V_solto=6250.0 m³ (dimensional ✓) |
| physical | ✓ | volumes_positivos=ok; empolamento=ok; movimentos_positivos=ok |
| normative | ✓ | balanco_consistencia=ok; situacao_consistente=ok; bota_fora_emprestimo=ok |

**Resultado da validaçêo:** APROVADO

### Avisos

- ⚠ Excesso de 2000.0 m³: prever bota-fora (descarte de solo).

---

⚠️ RESPONSABILIDADE TÉCNICA

Este cálculo é uma ferramenta de suporte à decisêo de engenharia. A responsabilidade técnica e legal é exclusiva do engenheiro responsável pelo projeto, formalizada via ART (Anotaçêo de Responsabilidade Técnica), conforme a Lei 6.496/77, a Lei 5.194/66 e a Resoluçêo CONFEA/CREA vigente.
