# Exemplo: Estabilidade de Talude — Talude Infinito

## Problema

Avaliar a estabilidade de um talude longo com manto de solo de pequena espessura sobre
camada mais resistente (ruptura plana paralela à face), pelo método do talude infinito:

- **Coesêo:** c = 10 kPa
- **Ângulo de atrito interno:** φ = 25°
- **Peso específico do solo:** γ = 18 kN/m³
- **Profundidade da superfície de ruptura:** z = 3,0 m
- **Inclinaçêo do talude:** β = 20°
- **Poro-pressêo na base:** u = 0 (sem percolaçêo)

## O que a engine calcula

1. Tensêo cisalhante resistente e atuante na base da fatia.
2. Fator de segurança FS = [c + (γ·z·cos²β − u)·tanφ] / (γ·z·sinβ·cosβ).
3. Classificaçêo: instável (FS < 1,0), crítico (1,0 ≤ FS < 1,5) ou estável (FS ≥ 1,5).

## Fora do escopo (abstençêo)

Bishop simplificado e Morgenstern-Price (métodos rigorosos com iteraçêo de FS), busca da
superfície crítica, análise probabilística, taludes reforçados e análise sísmica nêo sêo
tratados — princípio da abstençêo. Para ruptura circular use Fellenius (`talude_fellenius`),
que é conservador.

## Geraçêo

`solution.md` é gerado pela engine `lib.service.solve_infinite_slope`.

## Referência

Das, *Fundamentos de Engenharia Geotécnica*; NBR 11682 (FS,mín usual ≥ 1,5). Para os
dados acima: FS ≈ 1,86 (talude estável).
