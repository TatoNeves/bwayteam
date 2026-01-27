# Problema: WebSocket Mixed Content Error

## Sintoma
Após login, o frontend tenta conectar ao transactor via `ws://` (inseguro) ao invés de `wss://` (seguro):
```
Mixed Content: The page at 'https://bwayteam.com/workbench/broadway' was loaded over HTTPS, 
but attempted to connect to the insecure WebSocket endpoint 'ws://76.13.100.55/_transactor/...'
```

## Diagnóstico

### ✅ Configurações Corretas
1. Variáveis de ambiente no container `front`:
   - `TRANSACTOR_URL=wss://bwayteam.com/_transactor`
   - `TRANSACTOR_OVERRIDE=wss://bwayteam.com/_transactor`
   - `COLLABORATOR_URL=wss://bwayteam.com/_collaborator`
   - `LIVEKIT_WS=wss://bwayteam.com:7880`

2. NGINX configurado com SSL/TLS
3. Certificado Let's Encrypt válido
4. WebSocket headers no NGINX corretos

### ❌ Problema Identificado
O `/config.json` **NÃO** contém `TRANSACTOR_URL`:
```json
{
  "COLLABORATOR_URL":"wss://bwayteam.com/_collaborator",
  "LIVEKIT_WS":"wss://bwayteam.com:7880",
  "LOVE_ENDPOINT":"https://bwayteam.com/_love"
  // TRANSACTOR_URL está faltando!
}
```

## Código Relevante
No bundle.js do frontend (linha ~config.json handler), as variáveis são hardcoded:
```javascript
i.get("/config.json",async(P,L)=>{
  let M={
    ACCOUNTS_URL:e.accountsUrl,
    COLLABORATOR_URL:e.collaboratorUrl,
    LIVEKIT_WS:e.livekit_ws,
    // ... mas NÃO tem TRANSACTOR_URL na lista!
    ...n??{}  // extras podem ser passados aqui
  };
```

## Tentativas de Solução
1. ✅ Adicionado `TRANSACTOR_URL` ao environment do compose.yml
2. ✅ Adicionado `TRANSACTOR_OVERRIDE` 
3. ❌ Variável não aparece no config.json final

## Próximos Passos
1. Investigar como o frontend gera o config.json
2. Verificar se existe forma de adicionar variáveis extras
3. Verificar de onde vem o URL `ws://76.13.100.55` (IP do servidor)
