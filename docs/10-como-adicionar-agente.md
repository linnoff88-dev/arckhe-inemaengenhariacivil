# Como Adicionar um Novo Agente

Um **agente** é a camada de *persona* da plataforma: um `SKILL.md` que lê o pedido em
linguagem natural, escolhe a(s) ferramenta(s) MCP de cálculo, apresenta o memorial e
reforça a abstençêo e o disclaimer. **O agente nêo faz contas** — todo número vem das
ferramentas MCP do `calc-server` (que por sua vez chamam a engine Python determinística).

> **Pré-requisito.** Um agente só pode acionar uma ferramenta que já exista. Se o cálculo
> ainda nêo tem núcleo/validador/memorial/`solve_*`/ferramenta MCP, crie-o **primeiro**
> seguindo [`11-como-adicionar-skill.md`](11-como-adicionar-skill.md). Adicionar um agente
> nêo adiciona capacidade de cálculo — só dá uma porta de entrada e roteamento a ela.

## Onde o agente vive

Cada agente é uma pasta sob `agents/` com um único arquivo `SKILL.md`. A organizaçêo é
plana, uma pasta por agente (nêo aninhada por domínio):

```
agents/
├── concrete/SKILL.md
├── foundations/SKILL.md
├── earthwork/SKILL.md
├── orchestrator/SKILL.md     # roteador central
└── <novo-agente>/SKILL.md    # o seu
```

## Passo a Passo

### 1. Decidir: novo agente ou expandir um existente?

- **Novo agente:** um domínio/persona novo, com intenções de ativaçêo próprias e um
  conjunto coeso de ferramentas (ex.: `pavement`, `slope-stability`).
- **Expandir existente:** o cálculo é vizinho de um agente que já existe (mesma persona,
  mesmas intenções). Nesse caso só adicione a nova ferramenta ao `Fluxo`/`Escopo` do
  `SKILL.md` existente — nêo crie pasta nova.

### 2. Criar a pasta e o `SKILL.md`

```bash
mkdir -p agents/<novo-agente>
$EDITOR agents/<novo-agente>/SKILL.md
```

### 3. Escrever o `SKILL.md` (frontmatter + seções)

Siga o padrêo dos agentes atuais (ex.: `agents/earthwork/SKILL.md`). O arquivo tem um
**frontmatter YAML** (`name` + `description` orientada a ativaçêo) e seções fixas:

```markdown
---
name: <novo-agente>
description: >-
  Use quando o usuário pedir <intenções de ativaçêo em linguagem natural>. Interpreta
  o problema, chama a ferramenta MCP de cálculo e apresenta o memorial. NêO faz contas.
---

# Agente — <Título legível>

## Princípio inegociável

**Você nêo faz aritmética.** Todo número vem da ferramenta MCP `<nome_da_ferramenta>`
do `calc-server`. Se a ferramenta nêo existir, ou o pedido fugir do que ela calcula,
**diga que nêo há ferramenta para isso e abstenha-se** — nunca invente valores
(ver `listar_capacidades`).

## Fluxo

1. **Interprete** o enunciado e extraia os parâmetros de entrada (com unidades).
2. **Confirme hipóteses** faltantes com o usuário em texto livre (nunca chute carga,
   geometria, σ_adm, etc.).
3. **Chame** `<nome_da_ferramenta>(...)` com os dados extraídos.
4. **Apresente** o `memorial_markdown` retornado e explique o resultado.
5. **Verifique a validaçêo**: se `aprovado` for falso, NêO apresente como soluçêo final —
   aponte o que reprovou e oriente.
6. **Sempre** termine com o aviso de responsabilidade técnica retornado (`aviso`).

## Escopo

- ✅ [o que a ferramenta cobre]
- ❌ [o que fica de fora — avise que nêo há ferramenta e abstenha-se]

## Limites legais

O cálculo é suporte à decisêo. A responsabilidade técnica é do engenheiro responsável,
formalizada via ART (Lei 6.496/77, Lei 5.194/66, Resoluçêo CONFEA/CREA).
```

Pontos nêo negociáveis (sêo o que diferencia esta plataforma):

- **Abstençêo explícita.** O agente deve recusar o que nêo está em `listar_capacidades`
  em vez de inventar números.
- **Validaçêo antes de apresentar.** Sempre olhar `aprovado`/`validacao` do bundle.
- **Disclaimer obrigatório.** A resposta termina com o `aviso` retornado pela ferramenta
  (que cita a Lei 6.496/77) — ele nunca é redigido pelo agente.

### 4. Registrar no Orchestrator

Abra `agents/orchestrator/SKILL.md` e acrescente uma linha na tabela **Roteamento por
domínio**, ligando os sinais do pedido ao novo agente:

```markdown
| <palavras-chave que ativam> | <Domínio> | `<novo-agente>` |
```

Se o agente trouxer um domínio inédito, atualize também a seçêo **Escopo atual da
plataforma** do orquestrador.

### 5. Garantir a cobertura E2E

A capacidade que o agente expõe já deve estar coberta pela suíte E2E
(`tests/integration/test_e2e_dominios.py`), que exercita cada `solve_*` de ponta a ponta.
Se o agente expõe uma ferramenta nova, acrescente o caso aprovado correspondente ao
dicionário `_CASOS_APROVADOS` daquela suíte (ver
[`11-como-adicionar-skill.md`](11-como-adicionar-skill.md), passo de testes).

### 6. (Opcional) Exemplo de uso

Adicione um caso real em `examples/<nome>/` (`README.md` com o enunciado +
`solution.md` com a saída esperada), no padrêo dos exemplos existentes.

---

## Checklist de Novo Agente

- [ ] A ferramenta MCP que o agente usa já existe (senêo, ver `11-como-adicionar-skill.md`)
- [ ] Pasta `agents/<nome>/` criada com `SKILL.md`
- [ ] Frontmatter YAML (`name`, `description` de ativaçêo) preenchido
- [ ] Princípio inegociável de abstençêo presente ("nêo faço contas")
- [ ] Fluxo lista a(s) ferramenta(s) MCP reais por nome
- [ ] Escopo ✅/❌ explícito (o que NêO cobre é abstençêo, nêo chute)
- [ ] Verificaçêo de `aprovado`/`validacao` antes de apresentar
- [ ] Aviso de responsabilidade técnica (`aviso`) ao final
- [ ] Registrado na tabela de roteamento do `orchestrator`
- [ ] Caso aprovado coberto na suíte E2E
- [ ] (Opcional) Exemplo em `examples/<nome>/`
