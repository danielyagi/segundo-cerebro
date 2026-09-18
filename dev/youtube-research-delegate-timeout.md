# Delegate_task Timeout com YouTube Research (18/09/2026)

## Problema
Subagentes `delegate_task` com goal de research em YouTube timeout após 600s (10min) com 29-41 tool calls cada.

## Causa Raiz
- Subagentes tentam instalar yt-dlp, executar buscas, e YouTube retorna `Sign in to confirm you're not a bot`
- Cloud IP da VPS bloqueado para YouTube sem cookies autenticados
- Tentam browser_navigate → falha (sem CDP)
- Tentam skills (youtube-content, youtube-channel-research, social-video-content-analysis, youtube-transcription-cloud) → consomem tool calls sem sucesso
- **600s de timeout é o padrão do Hermes para subagentes** — não configurável sem alterar delegation settings

## Workaround
- **Não usar delegate_task para YouTube research em cloud IP**
- Fazer extração manual via curl + Python (HTML scraping de ytInitialData)
- Resultado: extrai títulos, descrições, capítulos em segundos, sem custo de API

## Técnica de Extração
```
curl -sL "https://www.youtube.com/watch?v=VIDEO_ID" -H "User-Agent: Mozilla/5.0" -o /tmp/video.html
python3 -c "
import re, json
with open('/tmp/video.html') as f: content = f.read()
ytdata = re.findall(r'var ytInitialData = ({.*?});', content, re.DOTALL)
data = json.loads(ytdata[0])
# Extrair título, descrição, capítulos
"
```

## Próximos Passos
- Para transcrições completas: Firecrawl CLI ou cookies autenticados do navegador Daniel
- Para análise em massa: script consolidado em vez de subagentes paralelos