# Lotofácil — Notificador de Sorteios Premium (R$ 10M+)

## Cronjob Reativado (31/08/2026)

Solução substituta para monitoramento de sorteios acumulados da Lotofácil.

### Fonte de Dados

- **Agregador público (confiável):** `https://loteriascaixa-api.herokuapp.com/api/lotofacil`
  - Retorna JSON completo do último concurso (concurso, dezenas, valorEstimadoProximoConcurso, acumulou, etc.)
  - Usa `urllib.request` — **zero dependências**, funciona sem crawl4ai/Scrapling
- **CAIXA oficial (`loterias.caixa.gov.br`):** bloqueada — edge Azion retorna 403 para IP 76.13.29.94 (IAD)
  - Nenhuma ferramenta de scraping funciona (curl, crawl4ai, Scrapling CDP)

### Arquitetura do Notificador

- **Script:** `/opt/data/scripts/lotofacil_premium.py`
- **Cronjob Hermes:** `lotofacil-premium-alert` (job_id: `77dfc207dcbc`)
- **Schedule:** `0 8,12,18,22 * * *` (4x/dia)
- **Modo:** `no_agent=True` — scheduler roda `.py` via Python diretamente, sem agente Hermes
- **Threshold:** R$ 10.000.000,00 (primeiro argumento, default)
- **Watchdog pattern:** silencioso quando abaixo do threshold — só imprime stdout quando ≥ R$ 10M

### Saída (stdout)

Quando ≥ threshold:
```
PREMIUM_ALERT: Lotofacil concurso 3775 — próximo acima de R$ 10.000.000,00: R$ 12.345.678,90. Acumulou: True
```

### SNR (Sinal/Ruído)

- Abaixo do threshold: **silêncio total** (stdout vazio = sem notificação)
- Acima do threshold: **linha única** com concurso, valor formatado BRL, status acumulou

### Problema Conhecido (não resolvido)

O scheduler `no_agent` reporta `last_status: ok` mas **não captura stdout** para entrega. Aparentemente stdout do processo `.py` roda em filesystem/contexto isolado. Alternativa pendente: script escrever em arquivo `/tmp/lotofacil_alert.txt` e cronjob agente ler.

### Histórico

- 27/08: primeira tentativa com crawl4ai (CAIXA oficial) — 403
- 30/08: Scrapling v0.4.15 testado — mesmo bloqueio
- **31/08:** migrado para agregador público, script `.py` sem dependências, cronjob `no_agent` reativado