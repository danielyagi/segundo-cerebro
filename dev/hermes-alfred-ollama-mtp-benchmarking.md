# Benchmarking Ollama com MTP e Otimização no Alfred

**Data:** 2026-10-11
**Contexto:** Sessão intensiva de ~590 mensagens testando performance do Ollama no Alfred VPS

## Ambiente

| Item | Valor |
|------|-------|
| VPS | bc6r (179.198.101.163) |
| CPU | AMD EPYC 9354P, 8 vCPUs |
| RAM | 31 GiB (sem GPU) |
| SO | Docker (Hostinger) |
| Modelo principal | `qwen3b-fast:latest` (Qwen2.5 3B Instruct Q4_K_M) |

## Mudanças Realizadas

1. **Ollama atualizado de 0.33.2 para 0.5.0**
   - Rollback salvo em: `/docker/ollama-1in7/docker-compose.yml.rollback-oldtag`
   - Versão fixada por digest

2. **Compose alterado com:**
   - `OLLAMA_NUM_PARALLEL=1` (confirmado não efetivo em CPU-only)
   - `OLLAMA_FLASH_ATTENTION=1` (logs mostram `flash_attn = 0` — CPU backend ignora)
   - `OLLAMA_KV_CACHE_TYPE=q8_0` (cache permaneceu F16, ignorado sem Flash Attention)
   - `OLLAMA_MAX_LOADED_MODELS=2`
   - `OLLAMA_MAX_QUEUE=20`

## Resultados de Performance

### Ollama 0.33.2 (referência)
- **Prompt curto (6.016 tokens, 64 saída):**
  - Cold: 91,6s total (prefill 70,4s / 85,4 tok/s)
  - Warm (repetição idêntica): 9,9s total (prefill cacheado 0,06s)
  - Geração: 17,14 tok/s
- **Contexto 65.536:** runner com `n_ctx=65536`, 2 slots de 32.768, ~11 GB RAM

### Ollama 0.5.0
- `qwen3b-fast:latest` com contexto 65.536:
  - Padrão (threads auto): ~15-16 tok/s (geração)
  - 4 threads: ~13-14 tok/s
  - 8 threads: ~17-18 tok/s (pico 33,3 em 0.33.2 com modelo não confirmado)
- **Nunca atingiu 30 tok/s consistentemente** em nenhuma configuração testada

### Cache de Prefixo
- Repetição idêntica se beneficia (~1s vs 10s)
- Prompts distintos: cold ~8,1s, warm ~8,8s (diferença pequena)
- Cache não dá ganho significativo em carga real de conversas variadas

### Flash Attention / KV Cache
- Ollama aceita as flags, mas em backend CPU `flash_attn = 0` e cache permanece F16
- **Flash Attention não funciona em CPU** — apenas GPU NVIDIA/AMD

## Conclusões

1. **Não reduzir contexto** global por causa de benchmarks
2. **Não fixar threads** — medições de 4/8 threads vs padrão são inconclusivas
3. **Ollama 0.5.0** funcionou, mas sem ganho perceptível sobre 0.33.2 em CPU
4. **Limite prático:** ~17-18 tok/s para Qwen 3B; 7B ~15 tok/s
5. **Vellum próximo do limite de memória** (2,96 GiB de 3 GiB)

## Scripts Gerados

- `/opt/data/ollama_eval.py` — avaliação de performance
- `/opt/data/ollama_parallel_probe.py` — probe de solicitações simultâneas
- `/opt/data/alfred_mtp_tests.py` — testes MTP com llama.cpp

## Referências

- `/opt/data/skills/devops/vps-container-app-operations/references/hermes-ollama-optimization-plan.md`
- `/opt/data/skills/devops/vps-container-app-operations/references/ollama-v032-specific.md`
- `/opt/data/skills/devops/vps-container-app-operations/references/alfred-ollama-keepalive-observations.md`