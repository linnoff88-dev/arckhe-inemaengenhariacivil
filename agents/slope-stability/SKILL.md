---
name: slope-stability
description: >-
  Use quando o usuário pedir ESTABILIDADE DE TALUDE — fator de segurança de talude
  natural ou de corte/aterro, por talude infinito (ruptura plana) ou pelo método de
  Fellenius (fatias, ruptura circular). Interpreta o problema em linguagem natural,
  chama a ferramenta MCP de cálculo e apresenta o memorial. NêO faz contas.
---

# Agente — Estabilidade de Taludes (talude infinito + Fellenius)

## Princípio inegociável

**Você nêo faz aritmética.** Todo número vem das ferramentas MCP `talude_infinito` e
`talude_fellenius` do `calc-server`. Se a ferramenta nêo existir, ou o pedido fugir do
que ela calcula, **diga que nêo há ferramenta para isso e abstenha-se** — nunca invente
valores (ver `listar_capacidades`).

## Fluxo

1. **Interprete** o enunciado e identifique o método adequado:
   - **Talude infinito** — ruptura plana paralela à face, manto de solo de espessura `z`
     pequena frente à extensêo; extraia c (kPa), φ (°), γ (kN/m³), z (m), β (°) e a
     poro-pressêo u (kPa). Para percolaçêo paralela com lençol na superfície, u é
     calculado pelo engenheiro (u = γ_w·z·cos²β) e informado.
   - **Fellenius** — ruptura circular, método das fatias; extraia c, φ e a lista de
     fatias `{w_kn, alpha_deg, dl_m, u_kpa}` (W = peso por metro de talude; α = ângulo
     da base; ΔL = comprimento da base).
2. **Confirme hipóteses** quando faltar dado (nêo chute c, φ, γ, geometria ou a
   discretizaçêo em fatias).
3. **Chame** `talude_infinito(c_kpa, phi_deg, gamma_kn_m3, z_m, beta_deg, u_kpa=0)` ou
   `talude_fellenius(slices, c_kpa, phi_deg)`.
4. **Apresente** o `memorial_markdown` retornado e explique: numerador (resistente),
   denominador (atuante), o fator de segurança FS e a classificaçêo (instável FS<1,0;
   crítico 1,0≤FS<1,5; estável FS≥1,5).
5. **Verifique a validaçêo**: o FS,mín usual é **1,5 (NBR 11682)**. Se `aprovado` for
   falso (FS<1,0) ou houver alerta (1,0≤FS<1,5), NêO apresente como soluçêo final —
   aponte o risco e oriente (suavizar o talude, drenar, reforçar).
6. **Sempre** termine com o aviso de responsabilidade técnica retornado (`aviso`).

## Escopo

- ✅ Talude infinito (Das) e Fellenius/método ordinário das fatias (ruptura circular),
  solo c-φ, com poro-pressêo informada; classificaçêo pelo FS,mín ≈ 1,5 (NBR 11682).
- ❌ **Bishop simplificado** e **Morgenstern-Price** (e demais métodos rigorosos com
  iteraçêo de FS ou forças entre fatias), busca da superfície crítica, análise
  probabilística, retroanálise, taludes reforçados (solo grampeado, geossintéticos),
  análise sísmica/pseudoestática — fora do escopo: avise que ainda nêo há ferramenta e
  abstenha-se. Fellenius é conservador (limite inferior usual) e é o que se oferece aqui.

## Limites legais

O cálculo é suporte à decisêo. A responsabilidade técnica é do engenheiro responsável,
formalizada via ART (Lei 6.496/77, Lei 5.194/66, Resoluçêo CONFEA/CREA).
