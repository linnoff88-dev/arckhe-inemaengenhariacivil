# Visêo Geral da Plataforma

## Objetivo

Construir um framework profissional de agentes de IA para engenharia de cálculos que possa crescer continuamente, permitindo adicionar centenas de novas skills e agentes sem necessidade de reestruturar o projeto.

## Decisêo Arquitetural Central

| Componente | Tecnologia | Responsabilidade |
|------------|-----------|-----------------|
| Cálculos numéricos | Python (numpy/scipy/sympy) | Toda a matemática |
| Verificaçêo dimensional | Python (pint) | Unidades e dimensões |
| Orquestraçêo | Claude Code Skills | Interpretaçêo, seleçêo de método |
| Bridge LLM ↔ Python | MCP Servers | Chamada de ferramentas |
| Documentaçêo | Markdown + PDF/Excel | Memorial, relatório |
| Persistência | JSON/YAML + SQLite | Projetos, histórico |

## Por que Python para cálculos?

1. **Auditabilidade:** cada linha de código é rastreável e revisável
2. **Confiabilidade:** sem alucinações numéricas (LLMs erram em aritmética)
3. **Testabilidade:** resultados comparáveis com referências de livros-texto
4. **Agnóstico ao modelo:** qualquer LLM com MCP pode usar o mesmo engine

## Fluxo Principal

```
Usuário → Agente Orquestrador → Agente Especialista
       → MCP Server → Python Engine → Validaçêo
       → Memorial + Excel + PDF + Checklist
       → Usuário (com aviso de responsabilidade)
```

## Documentos desta Pasta

| Arquivo | Conteúdo |
|---------|---------|
| `01-arquitetura.md` | Diagrama e camadas do sistema |
| `02-estrutura-diretorios.md` | Árvore completa de pastas |
| `03-agentes.md` | Lista e responsabilidades de cada agente |
| `04-skills.md` | Lista de skills independentes |
| `05-dependencias.md` | Todas as dependências e versões |
| `06-fases-implementacao.md` | Plano de implementaçêo em 6 fases |
| `07-estrategia-testes.md` | Como testar o sistema |
| `08-validacao-calculos.md` | Como validar cálculos de engenharia |
| `09-evolucao-futura.md` | Como expandir sem reescrever |
| `10-como-adicionar-agente.md` | Guia passo a passo |
| `11-como-adicionar-skill.md` | Guia passo a passo |
| `12-como-adicionar-norma.md` | Guia passo a passo |
