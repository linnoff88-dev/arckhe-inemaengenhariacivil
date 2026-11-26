> **Nota de escopo (gerado pela engine `lib.service.solve_square_footing`).**
> Este núcleo dimensiona sapata isolada quadrada **rígida sob carga centrada**
> (método das bielas). O momento característico `Mk = 20 kN·m` do enunciado do
> README é **desprezado**: sapata excêntrica (flexêo composta na base) está fora
> do escopo atual — princípio da abstençêo. Punçêo, cisalhamento e estabilidade
> (deslizamento/tombamento) também nêo sêo tratados aqui.
>
> **Resultado: REPROVADO pela validaçêo.** Com Nk = 600 kN e σ_adm = 150 kPa, o
> peso próprio real da sapata supera a estimativa de 5% e σ_solo ultrapassa
> ligeiramente σ_adm — o engenheiro deve **ampliar o lado** da sapata. O memorial
> é mantido para demonstrar a camada de validaçêo atuando.

# Memorial de Cálculo — Sapata Isolada Quadrada Rígida

**Norma:** NBR 6122 / NBR 6118:2014  
**Elemento:** Sapata isolada quadrada rígida — carga centrada (método das bielas)

## 1. Dados de entrada

| Parâmetro | Valor |
|-----------|-------|
| Carga normal característica Nk | 600.00 kN |
| Tensêo admissível do solo σ_adm | 150.0 kPa |
| Pilar (a × b) | 30 × 30 cm |
| Concreto | C25 (fck = 25 MPa) |
| Aço | CA-50 (fyk = 500 MPa) |

## 2. Geometria

- Área necessária: A_nec = 4.200 m²
- **Lado adotado: L = 205 cm** (área efetiva = 4.202 m²)
- Altura: h = 60 cm · altura útil d = 53.8 cm
- Sapata rígida (NBR 6118 22.6.1): sim

## 3. Tensêo no solo

- Peso próprio da sapata: pp = 63.04 kN
- **Tensêo no solo: σ_solo = 157.8 kPa** (σ_adm = 150.0 kPa)

## 4. Armadura de flexêo (método das bielas, NBR 6118 22.6.4.1)

- Força normal de cálculo: Nd = γf·Nk = 840.00 kN
- fyd = fyk/1,15 = 434.78 MPa
- As (direçêo x) = 7.86 cm² → **16 Ø 8.0 mm (As,ef = 8.04 cm²)**
- As (direçêo y) = 7.86 cm² → **16 Ø 8.0 mm (As,ef = 8.04 cm²)**

## 5. Validaçêo automática

| Verificaçêo | Status | Detalhe |
|-------------|--------|---------|
| units | ✓ | σ_solo=157.8 kPa (dimensional ✓) |
| physical | ✓ | sigma_adm=ok; materiais=ok; geometria=ok; As_positivo=ok |
| normative | ✗ | tensao_solo=fail; rigidez=ok |

**Resultado da validaçêo:** REPROVADO

### Avisos

- ⚠ Tensêo no solo σ=157.8 kPa > σ_adm=150.0 kPa (peso próprio real superou a estimativa de 5%): ampliar o lado da sapata.

---

⚠️ RESPONSABILIDADE TÉCNICA

Este cálculo é uma ferramenta de suporte à decisêo de engenharia. A responsabilidade técnica e legal é exclusiva do engenheiro responsável pelo projeto, formalizada via ART (Anotaçêo de Responsabilidade Técnica), conforme a Lei 6.496/77, a Lei 5.194/66 e a Resoluçêo CONFEA/CREA vigente.
