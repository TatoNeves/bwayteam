# Huly Self-Hosted Setup

## Informações do Servidor
- **Domínio**: bwayteam.com
- **IP**: 76.13.100.55
- **Versão Huly**: v0.7.350
- **SSL**: Let's Encrypt com auto-renovação

## Estrutura
- `compose.yml` - Docker Compose principal
- `.huly.nginx` - Configuração NGINX com SSL
- `livekit.yaml` - Configuração LiveKit
- Backups em `compose.yml.bak.*`

## Problema Atual
WebSocket Mixed Content: Frontend tentando conectar via `ws://` ao invés de `wss://`
