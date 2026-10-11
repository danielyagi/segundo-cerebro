# TableTennis Pro — App de Tênis de Mesa

**Data:** 2026-10-11
**Contexto:** Daniel quer criar um app Android/iOS para praticantes de tênis de mesa, com monetização via freemium + afiliação

## Features Planejadas

### MVP (3 meses)
1. **Placar digital** — contador touch-screen, histórico de sets, timer
2. **Biblioteca de vídeos** — 20-30 vídeos curados do YouTube por categoria
3. **Highlights de campeonatos** — links para WTT, Olimpíadas, CBT
4. **Guia de equipamentos (estático)** — tabela nível → raquete/borracha
5. **Treinos básicos** — 5 rotinas (iniciante → avançado)

### Full Version (6-12 meses)
1. **IA para recomendação de equipamento** — quiz estilo/experiência/orçamento
2. **Tracking de evolução** — win rate, gráficos, badges
3. **Planos de treino personalizados**
4. **Comunidade** — encontrar jogadores, ranking local
5. **Marketplace** — comprar equipamentos no app
6. **Modo torneio** — chaveamento, placar ao vivo

## Análise de Mercado

### 13 Concorrentes Identificados
- **Placar:** Table Tennis Scoreboard (FixM), Score Table Tennis, Scoreboard Pro, Voice, Minimal, Score Tracker
- **Tracking:** PingLog, Table Tennis Journal & Log
- **Comunidade:** Ping Pong Squad, World Table Tennis (ITTF oficial)
- **Gerenciamento:** Table Tennis Manager

### 5 Gaps de Mercado
1. App ALL-IN-ONE em português BR (apps existentes são fragmentados)
2. Recomendação de equipamento personalizada (inexistente)
3. Conteúdo educacional curado com progressão
4. Tracking de evolução + gamificação
5. Comunidade local BR

## Monetização

| Fase | Modelo |
|------|--------|
| 1 | Freemium (R$ 9,90/mês premium) |
| 2 | Afiliação lojas BR (5-10% comissão) |
| 3 | Marketplace |
| 4 | B2B clubes/academias |

## Projeção Financeira
- **Fase 0 (14 dias):** R$ 240 investidos
- **MVP (8 semanas):** R$ 1.244 investimento
- **Break-even:** Mês 6 (40 assinantes)
- **Receita Ano 1:** R$ 3.780/mês

## Validação (em andamento)
- Landing page criada em: `/opt/data/tt-landing/` (Next.js, 4 páginas)
- Deploy GitHub pendente
- Meta: 100 emails até 28/10/2026
- Decisão GO/NO-GO: 28/10

## Arquivos do Projeto
```
/opt/data/
├── tt-landing/                    ← Landing page Next.js
├── tabletennis_research/          ← Planejamento completo
├── RESUME_COMPLETO_FINAL.md      ← Resumo geral
├── DEPLOY_AGORA_INSTRUCOES.md    ← Instruções deploy
├── START_HERE.md                  ← Guia início rápido
└── ENTREGA_FINAL.md              ← Visão completa
```