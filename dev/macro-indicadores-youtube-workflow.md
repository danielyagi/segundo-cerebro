# Macro Indicadores: Extração de YouTube via Scraping HTML (18/09/2026)

## Contexto
Daniel solicitou mapeamento de indicadores macroeconômicos dos canais **Investidor Sardinha** (@investidorsardinha, Raul Sena) e **AUVP Capital** (@AUVPCapital) para embasar conteúdo de YouTube sobre métricas quant para mercado BR.

## Desafio: Cloud IP Bloqueado
- **yt-dlp** em cloud IP (i3a1/bc6r VPS) retorna: `Sign in to confirm you're not a bot`
- Solução alternativa: **curl + python** para parsear HTML bruto do YouTube
- Extrai do HTML: `ytInitialData` JSON → títulos, descrições, capítulos, likes, view count
- **Não extrai transcrições completas** sem cookies autenticados

## Workflow de Extração
1. `curl -sL "https://www.youtube.com/watch?v=VIDEO_ID" -H "User-Agent: Mozilla/5.0" -o /tmp/video.html`
2. Parse `var ytInitialData = ({...});` do HTML com regex
3. Navegar: `.contents.twoColumnWatchNextResults.results.results.contents`
4. Extrair: `videoPrimaryInfoRenderer.title`, `videoSecondaryInfoRenderer.attributedDescription`, `multiMarkersPlayerBarRenderer.markersMap[*].chapters`

## Resultados Principais

| Canal | Vídeo | Título | Teses Principais |
|-------|-------|--------|-----------------|
| Sardinha | uRrxDA9kOHY | SELIC A 14,25%: O BRASIL ESTÁ TRAVANDO | Selic alta → crédito caro → consumo travado → resiliência bolsa/fluxo estrangeiro |
| Sardinha | vDy1CtNXSjM | How to double your money - The SELIC rule at 15% | Regra dos 72, oportunidade LTN/NTN-B |
| AUVP | eTfSj5T9D20 | Copom eleva Selic, Fed corta juros | Spread DI x Treasuries, Risco Brasil (CDS), câmbio |
| AUVP | r1AzP4y2_IA | THE SELIC RATE CUT IS GIVING US A WARNING! | Curva invertida → recessão, inflação 2026, corte pré-eleições |
| AUVP | THIzBUbtgBA | RENDA FIXA: 5 dicas | Curva DI, NTN-B vs LTN, duration, marcação a mercado |
| AUVP | TZKF_0VdnM4 | Curva de juros: o que é | Estrutura a termo, abertura/fechamento/inversão |

## Indicadores Macro Mapeados (Recorrentes nos Canais)

| Categoria | Indicadores | Fontes |
|-----------|-------------|--------|
| Selic / Copom | Selic 13,75–14,25%, viés hawkish/dovish | BCB, Comunicados Copom |
| Curva DI / RF | DI1, spread 2y10y, forward rates, NTN-B, LTN | B3, Anbima |
| Inflação | IPCA, IGP-M, expectativas Focus, núcleos | IBGE, BCB Focus |
| Câmbio / Externo | USD/BRL, CDS 5y, Fed Funds, Treasuries 10y, DXY | Investing, Bloomberg |
| Fiscal | Dívida/PIB, primário, arcabouço, risco soberano | Tesouro, STN |
| Crescimento | PIB, IBC-Br, consumo, crédito, emprego | IBGE, BCB |

## Lição Aprendida
Delegate_task com 3 subagentes paralelos para YouTube research → timeout 600s cada (YouTube bloqueia cloud IP, subagentes gastam 40+ tool calls tentando alternativas sem sucesso). Abordagem mais eficiente: extração manual via curl+python que o próprio Hermes principal faz, economizando calls.