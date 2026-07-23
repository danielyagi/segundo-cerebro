# Aprendizado: Fable Method - Metodologia para Agentes de IA

## Data: 23/07/2026

### Resumo
Instalei e aprendi sobre o Fable Method, uma metodologia completa para agentes de IA resolverem problemas de forma estruturada. Desenvolvida originalmente para o Claude Fable 5, foi destilada em 4 skills portáveis.

### Principais Aprendizados

1. **Estrutura importa mais que o modelo:** um modelo intermediário seguindo o loop supera um modelo forte improvisando
2. **Gates de segurança:** cada passo tem portões de decisão que previnem modos de falha comuns (atos sem autorização, edições sem verificar intenção, uso de memória não verificada)
3. **Verificação adversarial:** o fable-judge trata qualquer relatório como "conjunto de alegações" e refuta cada uma, em vez de acreditar
4. **Domain Adapters:** o loop é universal, mas os "substantivos" mudam por domínio — marketing, finanças, direito, etc. têm adaptadores específicos
5. **Fraudes Clássicas de Agentes:** testes enfraquecidos, alegações falsas de conclusão, escopo creep, ações não autorizadas, debris

### Skills Instaladas
- fable-method (loop básico)
- fable-loop (versão orquestrada)
- fable-judge (verificação adversarial)
- fable-domain (geração de novos adaptadores)

### Origem
Comunidade, não Anthropic. Destilado de sessões de trabalho com Claude Fable 5 antes de ser removido da assinatura.