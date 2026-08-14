# Samsung DeX para Windows — Alternativas para Laptop Corporativo

**Data:** 14/08/2026
**Contexto:** Samsung S24 Ultra → Windows 11 laptop corporativo (sem permissão admin)
**Objetivo:** Wireless DeX funcional em laptop com restrições de instalação

## Status do Samsung DeX for Windows

- **Microsoft Store:** Removido — IDs `9NBLGGH5FMH5` e `9WPC8VLHDKF6` retornam 404
- **Instalador oficial ainda disponível** (verificado SHA256):
  - URL: `https://github.com/ntilau/DeX/releases/download/Mirror/SamsungDeXSetupWin.exe`
  - SHA256: `d5c96fd88b2758ceb806e0d2a1efdd5580b295bf1a563fc82b93e3548195058a`
  - ~100 MB

## Problema Corporativo

- Samsung DeX app exige **driver kernel Samsung** + permissões admin → **não funciona portable**
- Instalação bloqueada por AppLocker / group policy

## Alternativas Pesquisadas

### 1. Scrcpy (Portátil, Open-Source)
- **Funciona:** ADB wireless, 100% portátil, sem instalação
- **Limitação:** Espelhamento Android → Windows, **não tem layout DeX desktop**
- **Uso:** Mirror + controle via mouse/teclado

### 2. ADB + Intent Force DeX mode
- Técnica: forçar modo DeX via ADB shell commands
- Sem receiver Windows nativo

### 3. USB-C HDMI + Capturador
- DeX real via HDMI, capturador USB → Windows
- Não WiFi, precisa hardware extra

### 4. GitHub Projects
- `ntilau/DeX` — Mirror do instalador oficial (não open-source protocol)
- `maze-mei/DX-Manager` — ADB/scrcpy based, não DeX protocol real

## Conclusão

Para laptop corporativo sem admin:
- **Melhor opção real:** Scrcpy portable (ADB wireless) + layout customizado
- **Samsung DeX real:** Instalador oficial via GitHub mirror (se permitir admin temporário)
- **Não existe** receiver DeX protocol open-source funcional para Windows

## Links
- Scrcpy: https://github.com/Genymobile/scrcpy
- Samsung DeX installer mirror: https://github.com/ntilau/DeX/releases