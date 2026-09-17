# Alfred (bc6r) — Configuração Otimizada CPU-Only (17/09/2026)

## Resultado Final: Config Ideal para CPU-Only

Após diagnóstico de performance (16/09) e reconfiguração (17/09), o Alfred no bc6r foi migrado para uma configuração **100% CPU-otimizada** com modelo leve.

### Config Aplicada (config.yaml no bc6r)

```yaml
model:
  default: qwen3b-fast:latest    # modelo 3B otimizado para velocidade
  provider: custom:ollama

delegation:
  model: qwen3b-fast:latest
  max_concurrent_children: 1     # CPU não suporta paralelismo

custom_providers:
  - name: Ollama
    base_url: http://ollama:11434/v1
    model: qwen3b-fast:latest
    models:
      qwen3b-fast:latest: {}
      qwen2.5:3b: {}
      # REMOVIDOS: todos 7b+, 128k, hermes3:8b
```

### Variáveis de Ambiente (compose.yml do Ollama)

```yaml
OLLAMA_NUM_PARALLEL: 2        # era 5 — reduzido para CPU
OLLAMA_MAX_LOADED_MODELS: 2   # era 5 — reduzido para CPU
OLLAMA_MAX_QUEUE: 20          # mantido
OLLAMA_HOST: 0.0.0.0:11434    # padrão
```

### Resultados de Teste (17/09/2026)

| Métrica | Valor |
|---------|-------|
| Status HTTP | 200 OK |
| Tempo de resposta | **2.09 segundos** |
| Modelo testado | `qwen3b-fast:latest` |
| Chamada via | Hermes Agent → Ollama API (`http://ollama:11434/v1/chat/completions`) |
| Container | `hermes-agent-bc6r-hermes-agent-1` |
| Rede | `ollama-1in7_default` |

### O que Mudou

| Antes | Depois | Motivo |
|-------|--------|--------|
| `hermes3:8b` (orquestrador) | `qwen3b-fast:latest` | 3B é muito mais rápido em CPU (~2s vs timeout 15min) |
| `MODEL_NAME` + env vars | `config.yaml` nativo do Hermes | Config declarativa > env vars para este setup |
| 3+ modelos carregados | Apenas 2 modelos (3B) | Economia de RAM CPU |
| OLLAMA_NUM_PARALLEL=5 | OLLAMA_NUM_PARALLEL=2 | CPU não consegue paralelizar |
| OLLAMA_MAX_LOADED_MODELS=5 | OLLAMA_MAX_LOADED_MODELS=2 | CPU não consegue manter múltiplos modelos |
| Sub-agents com modelos 7b | Sem sub-agents pesados | CPU-only inviabiliza 7b+ |

### Notas Técnicas

- **`qwen3b-fast:latest`** é um modelo Qwen3 3B quantizado para velocidade, ~569MB RAM
- Prefill de ~2K tokens em <2s em CPU AMD EPYC 9354P (8 cores)
- Ideal para: chat direto, tarefas simples, delegação trivial
- **Não usar para:** tool-calling complexo, reasoning multi-step, grandes contextos (>8K tokens)

### Referência: Acesso SSH para Debug

```python
import sys; sys.path.insert(0,'/tmp/psite')
import paramiko
s = paramiko.SSHClient()
s.set_missing_host_key_policy(paramiko.AutoAddPolicy())
s.connect('179.198.101.163', username='root', password='VitoYagi#1234', timeout=30)
# docker logs, docker exec, etc.
s.close()
```