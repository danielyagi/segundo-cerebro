# JARVIS AI - Containerização para Docker

**Data:** 04/08/2026
**Categoria:** Desenvolvimento / DevOps

## Dockerfile

Criado para rodar o JARVIS server em container separado, isolado do Hermes.

```dockerfile
FROM hermes-agent:latest
WORKDIR /opt/data/jarvis_ai/server
COPY . .
RUN python3 -m venv .venv && .venv/bin/pip install --no-cache-dir -r requirements.txt
CMD ["/bin/bash", "-c", "LD_LIBRARY_PATH=/opt/data/tts_engine/piper/piper:$LD_LIBRARY_PATH .venv/bin/python tts_server.py & .venv/bin/python server.py"]
```

## docker-compose.yml (serviço adicionado)

```yaml
services:
  # ... serviços existentes ...
  jarvis:
    build: ./jarvis_ai/server
    container_name: jarvis-voice
    network_mode: host
    volumes:
      - /opt/data:/opt/data
    environment:
      - LD_LIBRARY_PATH=/opt/data/tts_engine/piper/piper
    restart: unless-stopped
```

## Considerações

- `network_mode: host` evita problemas de resolução de IP entre containers
- Montar `/opt/data` inteiro garante acesso a `.env`, certs TLS, e modelos TTS
- Container separado isola JARVIS do Hermes, permitindo restart independente
- `restart: unless-stopped` garante persistência após reboot

## Processo para Implantar

1. SSH no host da VPS (não dentro do container)
2. Parar processos JARVIS dentro do container Hermes (pkill -f "server.py|tts_server")
3. `docker compose up -d --build jarvis`
4. Verificar: `docker logs jarvis-voice -f`

## Pontos de Atenção

- Container não tem acesso Docker-in-Docker
- Se JARVIS rodar dentro do container Hermes (não separado), sobrevive apenas enquanto o container Hermes estiver ativo
- Porta 32772 no Hostinger proxy precisa apontar para a porta correta do JARVIS (8765 ou 443)