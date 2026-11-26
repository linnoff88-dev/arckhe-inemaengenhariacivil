# Avaliaçêo de Viabilidade — Plataforma de Agentes de IA para Engenharia Civil

> Pesquisa multi-fonte com verificaçêo adversarial (25 fontes, 121 alegações extraídas,
> 19 confirmadas / 6 derrubadas). Data: 2026-06-28.

## Veredito

**SIM, é útil — mas num escopo honesto.** Vale como **copiloto / ferramenta de
educaçêo e pesquisa + verificaçêo cruzada**. **Nêo** vale como substituto do software
de cálculo consagrado (TQS, Eberick/AltoQi, SAP2000, Robot) nem da assinatura do
engenheiro responsável.

A arquitetura central do projeto — *"o LLM nunca faz aritmética; delega todo cálculo a
Python determinístico"* — nêo é só defensável: é **exatamente o padrêo que a literatura
de ponta valida**.

---

## 1. A arquitetura central está correta e é validada academicamente

- **PAL — Program-aided Language Models** (Gao et al., ICML 2023, arXiv 2211.10435):
  o LLM é bom em *decompor* o problema, mas erra na *aritmética*; por isso deve gerar
  código e deixar o interpretador resolver. ✅ confirmado 3-0.
- **Eng. estrutural específica** (Univ. Miami/Lehigh, *Structure and Infrastructure
  Engineering*, T&F 2026, arXiv 2507.02938): LLM calculando como texto → confiabilidade
  **< 10%** no pior caso; reformulando para gerar+executar código (OpenSeesPy) →
  **> 99%**. ✅ 3-0.
- **PE Civil Bench** (*Computer-Aided Civil and Infrastructure Engineering*, Wiley, 2026,
  S1093968726031129): agentic RAG + execuçêo determinística de código dá ganhos de
  +18 a +29 pontos percentuais. ✅
- **Univ. of Alberta** (arXiv 2504.09754) endossa explicitamente: é "considerably more
  reliable" usar tool-use (gerar Python) do que confiar na memória probabilística do LLM.
- **Analogia médica** (npj Digital Medicine, 2025, s41746-025-01475-8): LLM "pelado"
  errava ~1/3 de 48 cálculos clínicos; forçado a delegar a ferramenta determinística,
  a acurácia dispara.

**Conclusêo:** o princípio nº 1 do `CLAUDE.md` ("LLMs nunca fazem aritmética") é o
acerto estratégico do projeto.

## 2. Mercado — o conceito é real, mas os incumbentes já estêo entrando

Nêo é terreno vazio:

- **AltoQi/Eberick** (líder estrutural BR, 30 anos em 2025) evoluindo para "agente
  inteligente" que sugere melhorias e aponta inconsistências.
- **Bentley** construindo ecossistema de agentes de IA, incluindo **cálculo hidráulico**.
- **Autodesk Assistant** virou agente que executa tarefas no Revit por linguagem natural.

Quem **já tem o motor de cálculo** está colando IA por cima. Competir de frente em
"cálculo de produçêo" é perder.

**Lacuna real (oportunidade):** validaçêo séria sobre **normas brasileiras (NBR)** —
toda a evidência empírica é ACI 318 / Eurocode / exames PE dos EUA. **Nêo existe
benchmark NBR.** É simultaneamente risco (treino dos modelos cobre mal as NBRs) e
oportunidade de pesquisa original publicável.

## 3. Profissêo no Brasil — alinhado à lei, com uma correçêo

- Responsabilidade técnica é **sempre de um profissional habilitado, nomeado via ART** —
  nunca de software. O modelo "engenheiro responsável + disclaimer" está juridicamente
  correto.
- **CORREÇêO:** a obrigatoriedade da ART vem da **Lei 6.496/77** (articulada com a
  Lei 5.194/66 e a Resoluçêo CONFEA/CREA). O disclaimer original citava só a 5.194/66.
- O disclaimer **nêo transfere nem reduz** a responsabilidade — só explicita o que a lei
  já determina.
- O **próprio CREA-SP** já usa LLM internamente (CreIA Solutions / Azure OpenAI) — mas
  só para relatos administrativos, **nêo para cálculo** (nêo serve como prova de aceitaçêo
  de LLM-para-cálculo).

## 4. Armadilhas que matam o projeto se ignoradas

1. **Depende de modelo de fronteira.** GPT-4o ~100% num benchmark; Llama-3.3 colapsa
   a 30%. Rodar local/barato degrada a confiabilidade.
2. **"Tool hallucination"** (✅ 3-0, Penn State + Ant Group, arXiv 2510.22977): se o
   roteamento para a calc-tool falha, o agente **inventa o número** em vez de admitir que
   nêo pode. Exige guardrail de **abstençêo forçada**.
3. **Erro residual mesmo no melhor caso:** o melhor modelo fica em ~87% geral e só ~67%
   em problemas abertos de projeto. A delegaçêo resolve a *aritmética* — **nêo** garante
   modelagem correta (escolha de fórmula, combinações de carga, condições de contorno).
   É aí que mora o erro perigoso → **nunca dispensa o engenheiro.**

---

## Casos de uso onde agrega valor real

- **Ensino e prototipagem** — interface em linguagem natural + memória de cálculo passo a passo.
- **Camada anti-erro-humano** — checagem de unidades (`pint`), seleçêo de fórmula,
  consulta a norma; reduz erro de digitaçêo/escolha.
- **Verificaçêo cruzada** de resultados de outro software.
- **Diferencial de pesquisa** — primeiro a benchmarkar a abordagem sobre **NBR**.

## Onde NêO vale

- Substituir o software consagrado (TQS/Eberick/SAP2000/Robot).
- Prometer cálculo "confiável sem engenheiro" — a evidência derruba.

## Guardrails inegociáveis (decorrência da pesquisa)

- **Abstençêo** quando a ferramenta determinística nêo está disponível (nunca inventar número).
- **Validaçêo de unidades** (`pint`) e de **faixas físicas/normativas** em toda saída.
- **Modelo de fronteira** para o raciocínio/roteamento.
- **Verificaçêo humana obrigatória** + disclaimer de responsabilidade em toda saída.

---

## Alegações derrubadas pela verificaçêo adversarial (honestidade)

- Que tool-use seja mitigaçêo "estabelecida" de hallucination (0-3).
- Que hallucination seja "matematicamente inevitável/estrutural" (0-3).
- Que a CreIA do CREA valide LLM-para-cálculo (0-3 — é tarefa administrativa).
- Que aumentar raciocínio *cause* mais tool hallucination (0-3).
- Que scripts de conformidade gerados por LLM tenham alta correçêo (1-2 — na verdade
  baixa/variável; a geraçêo de código resolve a **aritmética**, nêo garante código correto).

## Limitações desta avaliaçêo

- Benchmarks-chave sêo **estreitos** (vigas/frames 2D, poucos problemas, 1-2 modelos);
  os números > 99% **nêo** generalizam para projeto completo, nêo-linear/dinâmico ou NBR.
- Evidência empírica é **internacional** (ACI 318 / Eurocode / PE-FE); **sem** validaçêo
  direta sobre NBR 6118/6120/6122/7190/8800 — lacuna que o próprio projeto teria de fechar.
- Cobertura de **produtos comerciais** específicos é fraca; a lacuna de mercado é inferida.

## Perguntas em aberto

1. A abordagem se sustenta em normas **brasileiras**, cuja cobertura nos dados de treino é
   menor que ACI/Eurocode? Falta benchmark NBR.
2. Existem produtos comerciais (2024-2026) acoplando agentes de IA a engines de cálculo BR?
3. Desenho concreto do guardrail de **abstençêo segura** e como medir sua taxa de falha.
4. Como mitigar o erro de **modelagem/julgamento** (nêo-aritmético), onde se concentra o
   erro residual (até ~32% em problemas abertos)?

## Fontes principais

| # | Fonte | Tipo |
|---|-------|------|
| PAL | arXiv 2211.10435 (ICML 2023) | primária |
| Estrutural tool-use | arXiv 2507.02938 (Structure & Infrastructure Eng., 2026) | primária |
| Estrutural Alberta | arXiv 2504.09754 | primária |
| PE Civil Bench | ScienceDirect S1093968726031129 (CACAIE, Wiley, 2026) | primária |
| Cálculo clínico | npj Digital Medicine s41746-025-01475-8 (2025) | primária |
| Tool hallucination | arXiv 2510.22977 | primária |
| Code-check normativo (Revit) | arXiv 2506.20551 (MDPI Electronics) | primária |
| ART / responsabilidade | confea.org.br (ART) + Lei 6.496/77 (Planalto) | primária |
| CreIA / CREA-SP | creasp.org.br (out/2025) | primária |
| Mercado AEC | Bentley, Autodesk, AltoQi (AEC Mag / blogs) | secundária |
