# Cronjobs de Lotofácil — Cancelamento e Estado Atual

## Histórico

### `lotofacil-premium-alert` (Cancelado em 16/09/2026)

- **Schedule:** 0 8,12,18,22 * * * (4x/dia)
- **Script:** `lotofacil_premium.py` (no_agent)
- **Estado:** Removido em 16/09/2026 via `cronjob(action="remove", job_id="77dfc207dcbc")`
- **Motivo:** Usuário solicitou cancelamento diretamente

### `Lotofácil monitor — alerta quando próximo sorteio > R$ 10M` (Pausado desde 30/08/2026)

- **Schedule:** 0 10 * * *
- **Script:** `lotofacil_monitor.sh` (no_agent)
- **Estado:** Pausado (`enabled: false`)

## Cronjobs Ativos (Set/2026)

| Job | Schedule | Tipo |
|-----|----------|------|
| Segundo Cérebro - Salvamento Diário | 0 23 * * * | Agent (skill: segundo-cerebro) |

## Notas Técnicas

- Ambos cronjobs de Lotofácil eram `no_agent: true` (scripts shell/Python diretos)
- O monitor básico (`f285e2c5f912`) continua pausado, não removido — pode ser reativado