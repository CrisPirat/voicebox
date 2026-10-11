# Voicebox para agentes telefonicos con LLM

## Objetivo

Usar Voicebox como motor TTS dentro de un sistema de llamadas automatizadas donde una LLM conversa con clientes por telefono.

Este caso no necesita cambio de voz en vivo. Necesita un pipeline de agente de voz telefonico:

```text
Telefonia/SIP/WebRTC
-> STT en streaming
-> LLM
-> TTS
-> audio de vuelta a la llamada
```

## Rol de Voicebox

Voicebox puede usarse como motor TTS local:

```text
texto -> Voicebox /generate o /generate/stream -> audio WAV -> llamada
```

Voicebox no deberia ser toda la plataforma de callcenter. Lo recomendable es usarlo como una pieza dentro de un framework que maneje llamadas, turnos, interrupciones y streaming.

## Proyectos recomendados para validar

### Vocode

Repositorio:

```text
https://github.com/vocodedev/vocode-core
```

Uso esperado:

```text
Twilio/SIP/WebSocket -> STT -> LLM -> TTS adapter -> llamada
```

Ventajas:

- Enfocado en agentes de voz.
- Tiene integraciones con telefonia y streaming.
- Buen punto de partida para prototipos con llamadas reales.

Validacion con Voicebox:

- Crear un TTS adapter que llame a `http://127.0.0.1:17493/generate/stream`.
- Enviar texto generado por la LLM.
- Reproducir el audio devuelto dentro del call pipeline.

### Pipecat

Repositorio:

```text
https://github.com/pipecat-ai/pipecat
```

Uso esperado:

```text
Audio transport -> STT processor -> LLM processor -> Voicebox TTS processor -> output audio
```

Ventajas:

- Arquitectura flexible por processors.
- Buen enfoque para tiempo real.
- Adecuado para WebRTC, bots conversacionales y pipelines de audio.

Validacion con Voicebox:

- Implementar un processor TTS HTTP.
- Usar Voicebox como backend TTS local.
- Medir latencia primer audio y calidad conversacional.

### LiveKit Agents

Repositorio:

```text
https://github.com/livekit/agents
```

Uso esperado:

```text
LiveKit/WebRTC/SIP -> Agent session -> STT -> LLM -> Voicebox TTS -> room audio
```

Ventajas:

- Muy solido para WebRTC.
- Buen soporte para sesiones de voz en tiempo real.
- Buena opcion si la telefonia puede entrar por LiveKit SIP.

Validacion con Voicebox:

- Crear un plugin/adaptador TTS para Voicebox.
- Conectar llamadas SIP a LiveKit.
- Probar barge-in, interrupciones y latencia de respuesta.

## Arquitectura recomendada

Para una primera validacion seria:

```text
LiveKit Agents o Pipecat
-> STT local/cloud
-> LLM
-> Voicebox TTS local
-> audio a llamada
```

Para telefonia tradicional de callcenter:

```text
Asterisk o FreeSWITCH
-> streaming audio/ARI/ESL
-> STT
-> LLM
-> Voicebox TTS
-> audio a llamada
```

Para probar rapido con llamadas telefonicas reales:

```text
Vocode + Twilio + Voicebox TTS adapter
```

## Requisitos para produccion

- Latencia total de turno ideal: 1.5 a 3 segundos.
- STT parcial o streaming.
- LLM con primer token rapido.
- TTS con primer audio rapido o streaming por chunks.
- Barge-in: cortar a la IA si el cliente interrumpe.
- Deteccion de silencio y fin de turno.
- Logs y grabaciones.
- Manejo de errores y reintentos.
- Cumplimiento legal: consentimiento, aviso de grabacion y politicas de voz sintetica.

## Limitaciones actuales de Voicebox para llamadas

- Genera audio completo antes de devolverlo en muchos flujos.
- No maneja telefonia por si mismo.
- No maneja turn-taking ni barge-in.
- No es un servidor SIP/WebRTC.

## Mejor siguiente paso

Validar los tres frameworks en forks propios:

```text
vocodedev/vocode-core
pipecat-ai/pipecat
livekit/agents
```

Despues implementar un adaptador minimo para Voicebox TTS y medir:

- Tiempo hasta primer audio.
- Calidad de voz en llamada.
- Estabilidad de sesiones largas.
- Facilidad para conectar SIP/Twilio/LiveKit.
- Soporte de interrupciones.
