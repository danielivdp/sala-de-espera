# Sala de Espera

Panel de una sola página para vigías (monitores) que publican latidos a
[ntfy](https://ntfy.sh). Muestra, por vigía, si sigue corriendo y qué encontró
en su última pasada.

No tiene backend: lee el historial del topic con `fetch` directo a
`ntfy.sh/<topic>/json?poll=1&since=all`. La configuración (nombres, topics,
cadencias) vive **solo en el `localStorage` del navegador** — no está en este
repositorio ni viaja a ningún servidor.

## Formato del latido

Cada corrida del monitor publica al topic un mensaje con título `hb` y
prioridad `min` (queda en el historial, no notifica). El cuerpo es JSON:

```json
{
  "monitor": "ejemplo",
  "ts": "2026-08-31T21:25:56+00:00",
  "ok": true,
  "resumen": "0 dia(s) con cupo",
  "detalle": [{ "fecha": "2026-11-16", "horas": ["14:00", "14:30"] }],
  "error": "solo cuando ok es false"
}
```

Los mensajes con cualquier otro título se muestran como avisos.

## Configuración

Al abrirlo por primera vez pide un JSON:

```json
[
  {
    "nombre": "Nombre del vigía",
    "vigila": "Qué está esperando",
    "topic": "tu-topic-de-ntfy",
    "cadencia": 15,
    "horario": null
  }
]
```

- `cadencia`: minutos esperados entre corridas. Si el último latido supera
  2,5 veces ese valor, el vigía aparece **atrasado**; más de 7,5 veces, **sin señal**.
- `horario`: `[8, 21]` si el monitor solo corre en esa franja — fuera de ella el
  silencio se muestra como **en pausa**, no como falla.

También acepta la configuración por fragmento de URL (`#c=<json en base64>`),
que se guarda y se borra de la barra de direcciones. El fragmento nunca se envía
al servidor.

## Límite

ntfy retiene los mensajes 12 horas. El panel muestra esa ventana, no el
historial completo.
