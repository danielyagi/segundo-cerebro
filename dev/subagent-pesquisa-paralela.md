# Subagent Delegation para Pesquisa em Paralelo

**Data:** 14/08/2026
**Contexto:** Pesquisa Samsung DeX via delegate_task com timeout em primeira tentativa
**Tags:** delegate_task, subagent, pesquisa, paralelo

## Padrão: Pesquisa Delegada com Fallback

Quando uma pesquisa precisa de verificação em múltiplas fontes, usar delegate_task com escopo bem definido.

### Boas Práticas

1. **Escopo apertado:** Definir claramente o que o subagente DEVE e NÃO DEVE fazer
2. **Número de tool calls:** Especificar limite (ex: "20 tool calls max")
3. **Timeout:** Delegar ciente que subagentes têm timeout ~600s
4. **Fallback:** Se timeout, re-dispatch com escopo ainda mais restrito

### Padrão de Timeout Recovery

```python
# Tentativa 1: pesquisa ampla
subagent = delegate_task(goal="pesquisa ampla...", context="...")

# Se timeout: re-dispatch focado em subtask específica
subagent_retry = delegate_task(
    goal="Verificar APENAS URL X e Y",
    context="Falhou anteriormente. Escopo restrito."
)
```

### Exemplo: Verificação de Download Links

```python
delegate_task(
    goal="Verificar download links do Samsung DeX",
    context="""
        Windows laptop, Samsung S24 Ultra.
        Subtask ONLY: verificar URLs diretas.
        Usar curl/wget, sem JS.
        20 tool calls max.
    """
)
```

### Lições Aprendidas

- Subagentes que pesquisam muitos links podem timeout (600s)
- Múltiplas verificações HTTP consecutivas são lentas
- Dividir em: (a) pesquisa de links → (b) verificação individual
- Para verificação de download: foco em 2-3 URLs com SHA256