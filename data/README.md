# Base de preços (amostra)

## `sinapi_amostra.csv`

> **VALORES ILUSTRATIVOS.** Esta é uma amostra apenas para demonstraçêo e testes do
> domínio de Orçamento. Os preços **NêO** correspondem à SINAPI real e **devem ser
> substituídos pela tabela SINAPI vigente e regional** aplicável ao projeto
> (desonerada/nêo desonerada, mês de referência e UF), antes de qualquer uso em
> orçamento real.

Colunas: `codigo,descricao,unidade,preco_unitario`.

Carregada por `lib/budget/sinapi.py` (`load_sinapi` / `lookup`). Código inexistente na
base levanta `KeyError` (princípio da abstençêo: o agente nêo inventa preço).

A responsabilidade técnica e legal pelo orçamento é exclusiva do engenheiro/orçamentista
responsável (Lei 5.194/66 e Resoluçêo CONFEA/CREA vigente).
