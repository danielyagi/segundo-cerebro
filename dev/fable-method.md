# Fable Method - Metodologia Think/Act/Prove

## O que é

O **Fable Method** é uma metodologia de resolução de problemas em 7 passos (classificar → definir done → evidência → decidir → agir → verificar → reportar), destilada do comportamento do modelo Claude Fable 5.

## Skills Instaladas (23/07/2026)

| Skill | Descrição | Trigger |
|---|---|---|
| **fable-method** | Loop completo: classificar, definir done, evidência, decidir, agir, verificar, reportar | `/fable-method` |
| **fable-loop** | Versão orquestrada com subagentes paralelos e verificação adversarial | `/fable-loop` |
| **fable-judge** | Verificação adversarial de trabalho concluído — caça fraudes, testes enfraquecidos, escopo creep | `/fable-judge` |
| **fable-domain** | Gera novos adaptadores de domínio (adapter + workflow + trap + eval) | `/fable-domain` |

## Diferenciais da Metodologia

- **Triviality Gate:** tarefas triviais (1 arquivo, <10 linhas, sem busca) são resolvidas rápido, sem o loop completo
- **Fit Gate:** decide onde a resposta está (fontes acessíveis, técnica desconhecida, apenas inferência própria, ou procedimento especializado recorrente)
- **Intent Gate:** antes de qualquer edição comportamental, escreve `INTENT: code does X, check expects Y, spec says Z` — se não coincidirem, não edita
- **Recall Gate:** antes de usar qualquer coisa de memória, abre a fonte ou rotula como "memória, não verificado"
- **Authorization Gate:** ações irreversíveis (push, deploy, publish) precisam de citação textual do usuário
- **Verificação adversarial:** após o trabalho, spawna agentes para REFUTAR o resultado

## Domain Adapters Inclusos

8 adaptadores de domínio (traduzem o loop para substantivos específicos do setor):
- Marketing/Content
- Research/Reporting
- Data Analysis
- Business/Ops
- Finance
- Legal/Compliance
- Design/UX
- DevOps/Infrastructure

## Referências Disponíveis

- `failure-modes.md`: 18 modos de falha mapeados para o passo que previne cada um
- `examples.md`: exemplos completos para cada tipo de tarefa
- `flowcharts.md`: 8 fluxogramas de decisão executáveis
- `domains/TEMPLATE.md`: schema para criar novos adaptadores

## Como Usar

```
/fable-method <tarefa>        aplica o loop completo
/fable-method plan <tarefa>   para após o plano (passos 0-3)
/fable-method audit           avalia trabalho já feito contra o loop
/fable-judge                  verifica adversarialmente trabalho concluído
/fable-loop <tarefa>          versão orquestrada completa
/fable-domain <setor>         gera um bundle de adaptador para novo setor
```

## Origem

Repositório: https://github.com/Sahir619/fable-method
Licença: MIT