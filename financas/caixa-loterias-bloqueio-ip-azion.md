# Bloqueio CAIXA Loterias — Edge Azion (IP-based)

## Status atual (30/08/2026)
- **IP do servidor**: 76.13.29.94 (IAD)
- **Site**: `loterias.caixa.gov.br` — usa edge Azion (CDN)
- **Bloqueio**: Retorna HTTP 403 com "Your IP 76.13.29.94" para **qualquer** ferramenta de scraping
- **Ferramentas testadas**: curl, DuckDuckGo HTML, crawl4ai (CDP local), Scrapling (DynamicFetcher + CDP local) — todas 403
- **O bloqueio é por IP de saída**, não por fingerprint/navegador

## Workaround atual
- **Fonte alternativa**: `dezeneiro.com.br` (agregador de estimativas da CAIXA) — ainda funcional
- **Fallback**: DuckDuckGo HTML search (resultados orgânicos de terceiros)
- **Job Lotofácil**: pausado (`cronjob pause f285e2c5f912`) até resolver fonte

## Opções para resolver
1. **Proxy/VPN com IP brasileiro limpo** — rodar Chrome via proxy (fornecido pelo usuário)
2. **Browserless em datacenter não-bloqueado** — mas Docker sem daemon no VPS atual
3. **API de terceiros** — como `loterias-api.com` ou agregadores

## Histórico
- 27/08: crawl4ai instalado para bypass anti-bot
- 30/08: Scrapling instalado (v0.4.15) — mesmo resultado 403
- Conclusão: precisa de proxy com IP diferente