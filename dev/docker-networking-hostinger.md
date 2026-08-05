# Docker Networking na VPS Hostinger

**Data:** 04/08/2026 (atualizado 05/08/2026)
**Categoria:** Desenvolvimento / DevOps

## Problema

Ao rodar múltiplos containers Docker na VPS Hostinger (ex: Hermes + Firefox), containers não conseguem acessar serviços uns dos outros via IP da VPS (`172.16.1.2`).

## Causa

Cada container tem sua própria interface de rede. O IP `172.16.1.2` é da interface da VPS host, não da rede Docker interna. Containers só enxergam IPs da rede Docker (`172.17.0.X` ou similar) ou via `host.docker.internal` (se suportado).

## Soluções

### 1. Usar IP interno do container alvo
```bash
docker inspect <container-name> | grep IPAddress
# Saída: "172.17.0.X"
# Acessar: http://172.17.0.X:8765/
```

### 2. Usar host.docker.internal
```bash
# Se suportado pelo Docker da VPS:
http://host.docker.internal:8765/
```

### 3. Usar network_mode: host (se aplicável)
```yaml
services:
  jarvis:
    network_mode: host
    # Acessa serviços do host via 127.0.0.1
```

### 4. Usar docker-compose com network compartilhada
```yaml
networks:
  shared:
    external: true
```

## SSH Tunnel para Container (Descoberta 05/08/2026)

### Problema
`ssh -L 8787:localhost:8787` encaminha para porta do **HOST**, mas serviços rodam dentro do **container**. Resultado: `Connection refused`.

### Solução: Usar IP do container no tunnel
```bash
# Descobrir IP do container
cat /etc/hosts
# Exemplo: 172.16.1.2  e040958aa5f5

# Tunnel correto
ssh -L 8787:172.16.1.2:8787 root@76.13.29.94 -N
```

### Script mobile (Termux)
```bash
#!/data/data/com.termux/files/usr/bin/bash
VPS_IP="76.13.29.94"
VPS_USER="root"
CONTAINER_IP="172.16.1.2"   # IP do container
LOCAL_PORT="8787"
URL="http://localhost:${LOCAL_PORT}"

pkill -f "ssh.*${LOCAL_PORT}:${CONTAINER_IP}:${LOCAL_PORT}" 2>/dev/null
ssh -o StrictHostKeyChecking=no -o ExitOnForwardFailure=yes \
    -o ServerAliveInterval=10 \
    -L ${LOCAL_PORT}:${CONTAINER_IP}:${LOCAL_PORT} \
    -N ${VPS_USER}@${VPS_IP} &
```

### Verificação
```bash
# De dentro do container
cat /etc/hosts  # Mostra IP do container (ex: 172.16.1.2)

# Testar se porta está escutando no container
python3 -c "import socket; s=socket.socket(); s.settimeout(2); r=s.connect_ex(('172.16.1.2', 8787)); print('UP' if r==0 else 'DOWN'); s.close()"
```

## Proxy Hostinger (Portas Externas)

- Portas mapeadas no painel Hostinger vão para o container específico
- É necessário verificar qual porta interna cada porta externa mapeia
- Ex: `32772:4860` significa que porta externa 32772 → porta interna 4860 do container

## Ferramentas de Diagnóstico

```bash
# Verificar portas em LISTEN (de dentro do container)
python3 -c "
import socket
s = socket.socket()
s.settimeout(2)
r = s.connect_ex(('127.0.0.1', PORTA))
print('UP' if r==0 else 'DOWN')
s.close()
"

# Verificar /proc/net/tcp (todas as portas abertas)
python3 -c "
with open('/proc/net/tcp') as f:
    next(f)
    for line in f:
        parts = line.split()
        port = int(parts[1].split(':')[1], 16)
        state = parts[3]
        print(f'Port {port}: state={state}')
"

# Verificar interfaces de rede
ls /sys/class/net/
cat /sys/class/net/eth0/operstate
```