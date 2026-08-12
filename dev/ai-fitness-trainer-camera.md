# AI Personal Trainer com Câmera: Soluções e Tecnologias

**Data:** 12/08/2026
**Fonte:** YouTube, Y Combinator, Google MediaPipe
**Contexto:** Pesquisa de soluções de IA coach com monitoramento por câmera para correção de forma em tempo real

## Soluções Comerciais

### BodyPark ATOM
- **Tipo:** Dispositivo físico portátil ("pocket-sized AI fitness coach")
- **Recursos:** Tracking de movimento em tempo real, correção de postura, sessões de alongamento, integração workspace
- **Prêmios:** Red Dot Award
- **Status:** Produto comercial lançado

### Kinetic AI
- **Tipo:** App/SaaS
- **Recursos:** Live AI rep counting & form coaching

### Formcheck AI / F/AI
- **Apps:** "Form AI", "FitAI Coach", "Rep AI"
- **Recursos:** Correção instantânea de forma, feedback em tempo real

## Tecnologias Open Source para Desenvolvimento Próprio

### 1. Google MediaPipe Pose
- **Leve, ideal para edge computing**
- **Docs:** https://developers.google.com/mediapipe/solutions/vision/pose_landmarker
- **GitHub:** https://github.com/google/mediapipe
- **Funcionalidades:** 33 landmarks corporais, detecção em tempo real, mobile-friendly

### 2. MoveNet (TensorFlow)
- **GitHub:** https://github.com/tensorflow/models/tree/master/official/projects/movenet
- **Otimizado para:** Dispositivos móveis e browsers
- **Modelos:** Lightning (rápido) / Thunder (preciso)

### 3. Ultralytics YOLO Pose
- **GitHub:** https://github.com/ultralytics/ultralytics
- **Modelos:** YOLO11/YOLO12-Pose
- **Recursos:** Detecção + pose em uma única inferência

### 4. OpenPose (CMU)
- **GitHub:** https://github.com/CMU-Perceptual-Computing-Lab/openpose
- **Mais pesado, mas muito preciso**
- **Ideal para:** Pesquisa, análise offline

## Arquitetura Típica

```
Câmera/Webcam → Pose Estimation (MediaPipe/YOLO) → IA Coach → Feedback (TTS/áudio/texto)
```

## Funcionalidades Esperadas

- ✅ Correção de forma em tempo real
- ✅ Contagem automática de repetições
- ✅ Análise de ritmo e sequência
- ✅ Feedback verbal (TTS)
- ✅ Monitoramento de postura
- ✅ Histórico de progresso

## Integração com Hermes Agent

- **MediaPipe + Python:** Pode rodar localmente no Hermes
- **TTS:** edge-tts (pt-BR-AntonioNeural já configurado) para feedback em português
- **Câmera:** Acesso via OpenCV (Python) ou browser automation