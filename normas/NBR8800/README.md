# NBR 8800:2008 — Projeto de Estruturas de Aço e de Estruturas Mistas

## Metadados

- **Código:** ABNT NBR 8800
- **Ediçêo:** 2008 (2ª ediçêo)
- **Título:** Projeto de estruturas de aço e de estruturas mistas de aço e concreto de edifícios
- **Status:** Em vigor

## Tabelas Disponíveis

| Arquivo | Conteúdo |
|---------|---------|
| `tables/perfis_w.json` | Perfis laminados W (abas paralelas) — 14 bitolas |
| `tabela_perfis_I.json` | Perfis laminados I (W, HP) |
| `tabela_perfis_L.json` | Cantoneiras simples e duplas |
| `tabela_perfis_U.json` | Perfis U (UNP, UDN) |
| `tabela_parafusos.json` | Resistências de parafusos A325 e A490 |

### `tables/perfis_w.json` (Fase 5 — perfis e estabilidade)

- **Fonte:** Catálogo Gerdau Açominas — *Perfis Estruturais* (bitolas W).
- **Campos por perfil:** `d_mm, bf_mm, tw_mm, tf_mm` (seçêo, mm); `area_cm2`;
  `ix_cm4, iy_cm4`; `wx_cm3, wy_cm3` (módulos elásticos); `zx_cm3` (módulo
  plástico); `rx_cm, ry_cm` (raios de giraçêo); `massa_kg_m`.
- **Consistência interna:** `rx ≈ √(Ix/A)` e `ry ≈ √(Iy/A)` (verificada em
  `tests/unit/test_steel_profiles.py`, tolerância de arredondamento de catálogo).
- **Uso:** flambagem de barra comprimida (`lib/steel/stability.py`, §5.3) usa o
  eixo de menor inércia (`Iy`).

## Seções Mais Utilizadas

| Seçêo | Assunto |
|-------|---------|
| §5 | Barras submetidas à traçêo |
| §5.3 | Barras submetidas à compressêo e flambagem |
| §5.4 | Vigas: flexêo e flambagem lateral |
| §5.5 | Barras submetidas à flexo-compressêo |
| §6.2 | Ligações soldadas |
| §6.3 | Ligações parafusadas |

## Aços Usuais

| Aço | fy (MPa) | fu (MPa) |
|-----|---------|---------|
| ASTM A36 | 250 | 400 |
| ASTM A572 Gr50 | 345 | 450 |
| AR350 (ABNT) | 350 | 450 |
| AR415 (ABNT) | 415 | 520 |
