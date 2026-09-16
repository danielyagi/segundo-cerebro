# Alfred (bc6r) — Diagnóstico de Performance Ollama CPU-Only

## Data: 16/09/2026

### Problema Detectado
Ollama no bc6r retornava HTTP 500 após **15 minutos de timeout**:
- `POST /v1/chat/completions` com **16.910 tokens de input**
- Task cancelada pelo gateway Hermes após 15 min
- `qwen2.5:3b` não conseguiu gerar resposta a tempo

### Hardware Diagnosticado
- **CPU:** AMD EPYC 9354P — 8 cores, **sem GPU**
- **RAM:** 31GB (4.5GB em uso)
- **Inferência:** 100% CPU
  - Prefill de 16K tokens: ~3-8 min
  - Geração: ~50-200ms/token

### Problemas Encontrados

| # | Problema | Impacto |
|---|----------|---------|
| 1 | **qwen2.5:3b fraco para agent work** | 3B params falha em tool-calling multi-step |
| 2 | **CPU-only + 16K tokens** | Prefill 3-8 min + geração 5-15 min = timeout |
| 3 | **custom_providers.models com 12 modelos** | Ollama carrega modelos 7b+ desnecessários |
| 4 | **OLLAMA_NUM_PARALLEL=5** | Requests simultâneos em CPU = fila/timeout |
| 5 | **streaming disabled** | Hermes espera resposta completa |

### Recomendação: Setup Híbrido (Cloud + Local)

```yaml
model:                         # principal = cloud
  default: openrouter/qwen/qwen3-8b
  provider: openrouter

delegation:                    # filhos = local leve
  model: qwen3b-fast:latest
  provider: custom:ollama
  max_concurrent_children: 1
```

- **1 child apenas** para modelo local (CPU não suporta paralelismo)
- Qwen3 8B tem raciocínio híbrido (thinking) mas **não tem visão** (Qwen3-VL é separado)
- Modelos locais servem apenas para: delegação trivial, embeddings, tarefas sem reasoning
- Custo cloud estimado: ~$0.50-2/mês para uso típico

### Próximos Passos (Pendentes)
- Usuário decidiu fazer apenas com modelos locais (não cloud)
- Aguardando OPENROUTER_API_KEY no `.env` do bc6r se optar por cloud
- Opção alternativa: `qwen3-vl:8b` (raciocínio + visão, ~6GB RAM) se puxar novo modelo