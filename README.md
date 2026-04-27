# @srlczinn/new-baileys

Fork do Baileys baseado na base do GitHub `WhiskeySockets/Baileys`, com ajustes de compatibilidade para fluxos que pararam de funcionar em versões recentes.

## O que esta versão traz

- compatibilidade extra para envio de botões e mensagens interativas
- suporte legado para campos como `buttons`, `templateButtons`, `sections`, `buttonText` e `interactiveButtons`
- helpers expostos no socket para conversão entre PN/JID e LID:
  - `sock.getLIDForPN(pn)`
  - `sock.getLIDsForPNs(pns)`
  - `sock.getPNForLID(lid)`
- `onWhatsApp()` ajustado para consultar PN/JID e LID no mesmo fluxo e retornar:
  - `jid`
  - `lid`
  - `exists`

## Instalação pelo GitHub

```bash
npm i github:srlczinn/new-baileys
```

Também pode usar a URL completa:

```bash
npm i https://github.com/srlczinn/new-baileys.git
```

## Import

```ts
import makeWASocket from '@srlczinn/new-baileys'
```

Ou com imports nomeados:

```ts
import makeWASocket, {
  useMultiFileAuthState,
  makeCacheableSignalKeyStore,
  DisconnectReason,
  Browsers,
  proto
} from '@srlczinn/new-baileys'
```

## Exemplo básico

```ts
import makeWASocket, {
  useMultiFileAuthState,
  makeCacheableSignalKeyStore,
  fetchLatestBaileysVersion
} from '@srlczinn/new-baileys'
import pino from 'pino'

const logger = pino({ level: 'silent' })
const { state } = await useMultiFileAuthState('./auth')
const { version } = await fetchLatestBaileysVersion()

const sock = makeWASocket({
  version,
  logger,
  auth: {
    creds: state.creds,
    keys: makeCacheableSignalKeyStore(state.keys, logger)
  }
})
```

## Exemplo de botões legados

```ts
const buttons = [
  { buttonId: 'id1', buttonText: { displayText: 'Button 1' }, type: 1 },
  { buttonId: 'id2', buttonText: { displayText: 'Button 2' }, type: 1 }
]

await sock.sendMessage(jid, {
  text: 'Oi, essa mensagem tem botões',
  footer: 'New Baileys',
  buttons,
  viewOnce: true
})
```

## Exemplo de botões interativos/native flow

```ts
await sock.sendMessage(jid, {
  text: 'Escolha uma opção',
  footer: 'New Baileys',
  interactiveButtons: [
    {
      name: 'quick_reply',
      buttonParamsJson: JSON.stringify({
        display_text: 'Responder',
        id: 'responder_1'
      })
    }
  ],
  viewOnce: true
})
```

## Exemplo de helpers LID/JID

```ts
const lid = await sock.getLIDForPN('5511999999999@s.whatsapp.net')
const pn = await sock.getPNForLID('1234567890@lid')
const mappings = await sock.getLIDsForPNs([
  '5511999999999@s.whatsapp.net',
  '5511888888888@s.whatsapp.net'
])
```

## Exemplo do onWhatsApp com LID

```ts
const results = await sock.onWhatsApp(
  '5511999999999@s.whatsapp.net',
  '1234567890@lid'
)

console.log(results)
// [
//   { jid: '5511999999999@s.whatsapp.net', lid: '...', exists: true },
//   { jid: '1234567890@lid', lid: '...', exists: true }
// ]
```

## Observações

- esta biblioteca é um fork comunitário e não é o pacote oficial do projeto WhiskeySockets
- o código-fonte vem da base do GitHub e as alterações ficam em `src/`, para o build gerar `lib/` corretamente
- recomenda-se travar a versão no seu projeto para evitar perder compatibilidade em atualizações futuras

## Licença

MIT
