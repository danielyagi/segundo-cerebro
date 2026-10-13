# Alfred (bc6r) — Fix de Tool-Calling do Modelo Local 3B (13/10/2026)

## Problema

Alfred roda modelo compacto ~3B (100% CPU) via Ollama. A taxa de geração (t/s) está
adequada e a temperatura está fixada em 0.1, **mas o agente falha criticamente em
tool calling e execução de scripts**.

### Gargalos identificados

1. **Contexto muito baixo (<8k) é recusado na inicialização do Hermes**;
   contexto expandido (64k) **dilui severamente a atenção do modelo 3B**,
   fazendo-o ignorar schemas de ferramentas.
2. O bootstrap nativo do Hermes carrega dezenas de skills e schemas JSON pesados,
   esgotando o limite cognitivo do modelo.

## Alvos confirmados na sessão

| Item | Valor |
|---|---|
| Host correto | `179.198.101.163` (bc6r) |
| Container Ollama | `ollama-1in7-ollama-1` |
| Modelos alvo | `llama3.2:3b` e `qwen3b-fast:latest` (este é o "qwen3" autorizado) |
| Meta de contexto | `num_ctx=16384` em ambos |
| Blobs Ollama | `/root/.ollama/models/blobs/` (no container) |

## O que FALHOU (13/10)

**Edição direta dos blobs de params do Ollama não funciona:**
- ao localizar o blob esperado do `llama3.2:3b` e tentar sobrescrever o arquivo,
  o processo parou com `MISSING_BLOB` para o digest
  `56bb8bd477a519ffa694fc449c2413c6f0e1d3b1c88fa7e3c9d88d3ae49d4dcb`;
- o manifest do `llama3.2:3b` foi interpretado com digest em formato errado;
- nenhuma alteração foi aplicada — a sessão parou com segurança e sem afirmar
  conclusão falsa.

**Lição:** não editar blobs/manifests do Ollama à mão. É frágil e propenso a
corromper o modelo.

## O que FUNCIONA (12/10) — aliases via Modelfile

A abordagem que já provou funcionar no Alfred é **criar aliases via Modelfile**:

```bash
# 1. Modelfile no host
cat > /root/Modelfile.<alias> <<'EOF'
FROM <modelo-base>
PARAMETER temperature 0.1
EOF

# 2. Copiar para o container e criar o alias
docker cp /root/Modelfile.<alias> ollama-1in7-ollama-1:/tmp/Modelfile.<alias>
docker exec ollama-1in7-ollama-1 ollama create <alias> -f /tmp/Modelfile.<alias>

# 3. Verificar
docker exec ollama-1in7-ollama-1 ollama show <alias> | sed -n '1,40p'
```

### Aliases já criados no Alfred (12/10/2026, temperature 0.1 confirmada via `ollama show`)

- `llama3.2:3b` (base)
- `llama3.2:3b-65k`
- `llama3.2:3b-65k-t01`
- `qwen2.5-coder-7b-t01`
- `qwen2.5vl-7b-t01`
- `hermes3-8b-t01`

## Próximo passo recomendado

Em vez de editar blobs: criar aliases com `PARAMETER num_ctx 16384` (junto com
`temperature 0.1`) via Modelfile para `llama3.2:3b` e `qwen3b-fast:latest`,
verificar com `ollama show`, e então apontar o `config.yaml` do Hermes (bc6r,
`/opt/data/config.yaml`) para o alias ajustado.

## Notas

- O objetivo do ajuste é achar o ponto ótimo de contexto: alto o suficiente para o
  bootstrap do Hermes (>= 8k) e baixo o suficiente para o 3B manter atenção nos
  schemas de ferramentas (64k é demais). `16384` é o alvo escolhido.
- Config CPU-only anterior do Alfred: ver `hermes-alfred-ollama-cpu-optimized.md`
  (17/09/2026) — modelo default `qwen3b-fast:latest`, provider `custom:ollama`,
  `OLLAMA_NUM_PARALLEL=2`, `OLLAMA_MAX_LOADED_MODELS=2`.