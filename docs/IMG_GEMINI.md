# IMG-GEMINI — Generación de láminas de curso con Gemini image

**Estado:** F1 (investigación) hecha · F2 (UI) **bloqueada por decisión de billing de Gustavo**.
**Pedido:** Gustavo, 2026-06-22. Responsable diseño: Claude.

---

## Por qué existe (requisito cuestionado — paso 1 del mantra)

No es un capricho: el backend local **A1111** (Realistic Vision en una GPU de 3.94 GB)
**no da la talla** para ilustrar los cursos. Medido sobre los manifiestos reales
(`backend/data/illustration-manifests/`):

| Curso | Total láminas | done | rejected | pending |
|-------|--------------:|-----:|---------:|--------:|
| **cc-marina** | 120 | 24 | **96 (80%)** | 0 |
| cc-terrestre | 215 | 215 | 0 | 0 (despublicado por calidad) |
| aves | 70 | 0 | 0 | 70 |
| foto-fauna | 105 | 0 | 0 | 105 |
| nudibranquios-cat | 70 | 0 | 0 | 70 |

Motivos de rechazo típicos de A1111 (del propio `verifyIssues`): "muestra un automóvil",
"astronauta en paisaje nevado", "personaje de fantasía", "texto ilegible / gibberish",
"no está en castellano", OOM CUDA. Es decir: **ni el contenido ni el texto en español**
salen bien con el modelo local. La prueba de Gustavo con Gemini (diagrama fotorealista
de especies del Mediterráneo) demostró calidad muy superior.

➡ El requisito es válido. Hay ~341 láminas pendientes/rechazadas que justifican el cambio de backend.

---

## F1 — Investigación (HECHA 2026-06-22)

- El `GEMINI_API_KEY` actual **y** `GEMINI_API_KEY2` **SÍ tienen acceso** a los modelos de imagen:
  `gemini-3.1-flash-image` (= **Nano Banana 2**), `gemini-3-pro-image`,
  `nano-banana-pro-preview`, `gemini-2.5-flash-image`, `imagen-4.0-*`.
- **PERO la cuota gratuita de generación de imagen está agotada** (HTTP 429 "exceeded quota"
  en ambos keys y ambos modelos).
- ➡ **Generar imágenes con Gemini requiere activar BILLING** en Google AI Studio.
  Coste estimado bajo (~10 img/día). **Decisión pendiente de Gustavo.**
- Modelo por defecto propuesto: **Nano Banana 2** (`gemini-3.1-flash-image`).
  Pruebas 3.1 vs 2.5 quedan para cuando haya cuota.

---

## F2 — UI de revisión humana (diseño cerrado, a implementar tras billing)

### Modelo de datos: REUTILIZAR el manifiesto existente

Ya existe `backend/data/illustration-manifests/<curso>.json` con la forma exacta que
necesitamos. **No se inventa tabla nueva**: se extiende el item con campos de Gemini.

Item actual: `key, courseId, day, slide, dayTitle, slideTitle, status, alt, outputAbs,
generatedAt, backend, bytes, verifyStatus, verifyIssues`.

Campos a añadir (versionado por lámina, sin borrar rechazadas):
```jsonc
{
  "promptSuggested": "…",          // prompt que propone Claude (1 por lámina)
  "promptUsed": "…",               // el que se ejecutó realmente (editable por Gustavo)
  "backend": "gemini",             // pasa de "a1111" a "gemini"
  "model": "gemini-3.1-flash-image",
  "status": "suggested|queued|generating|review|confirmed|rejected",
  "versions": [                    // historial: nunca se borra, se puede comparar/volver atrás
    { "v":1, "prompt":"…", "outputAbs":"…", "bytes":…, "createdAt":"…", "verdict":"rejected", "note":"…" }
  ],
  "activeVersion": 2               // cuál está confirmada / en revisión
}
```

### Flujo (acordado con Gustavo 2026-06-22)

1. **Claude genera prompts sugeridos** — 1 por lámina del curso (`promptSuggested`),
   basados en `slideTitle` + `dayTitle` + `script` real del guion.
   ✅ **Hecho para cc-marina**: [`img-gemini-prompts-cc-marina.json`](img-gemini-prompts-cc-marina.json)
   — 86 láminas ilustrables → **30 prompts madre** (las escenas repetidas comparten prompt),
   estilo **ilustración científica naturalista**, con `stylePrefix` + `negative` comunes.
   Cobertura 100% de content+recap; las `task`/`closing` no llevan ilustración.
2. Gustavo revisa en la **UI de galería** (tarjetas, NO tabla densa): por lámina ve
   **imagen + texto del curso resumido al lado** + el prompt.
3. Acciones por lámina: **aceptar** · **editar prompt** · **rechazar** · **relanzar**.
4. **Generar de 1 en 1** — los límites de Gemini image obligan a una imagen por petición.
   NO variantes simultáneas; si se quiere otra opción, relanzar tras revisar.
5. **Estados visibles + cola**: suggested → queued → generating → review →
   confirmed / rejected. Cola visible (qué se está generando / qué espera).
6. **Historial de versiones** por lámina: las rechazadas no se borran; se puede comparar
   y volver atrás.
7. Cada lámina **confirmada** pasa al manual del curso y baja al final de la tabla;
   editable más adelante.

### Backend (a implementar)

- Nuevo backend `gemini` en el generador de ilustraciones (junto a `a1111`):
  POST a `generativelanguage.googleapis.com/v1beta/models/<model>:generateContent`
  con la imagen en `inlineData`. Respeta **1 petición a la vez** (cola serializada).
- Throttle/espera entre peticiones según límites del plan de pago (definir al activar billing).
- Endpoints admin (protegidos `require_admin`):
  - `GET  /admin/illustrations/<curso>` — manifiesto con estados.
  - `POST /admin/illustrations/<curso>/<key>/suggest` — (re)genera promptSuggested.
  - `POST /admin/illustrations/<curso>/<key>/generate` — encola 1 generación (prompt opcional).
  - `POST /admin/illustrations/<curso>/<key>/verdict` — confirmar/rechazar versión.
- Registro de auditoría: toda acción (generar, confirmar, rechazar, editar prompt) con actor.

### UI (a implementar)

- Sección nueva en panel admin (FF o BQ): **galería de láminas** por curso.
- Tarjeta = imagen grande + slideTitle/dayTitle + prompt editable + estado + botones.
- Indicador de cola arriba (N en cola, 1 generando).
- Selector de modelo (Nano Banana 2 por defecto; permitir 2.5 para comparar).

---

## Decisiones pendientes de Gustavo

1. **¿Activar billing de Gemini?** (sin esto, F2 no arranca). Coste ~10 img/día = bajo.
2. ¿En qué panel vive la UI — FotoFauna o BioQuest? (el manifiesto sirve a cursos de BQ Academy → propongo **BQ**).

---

_Última actualización: 2026-06-22._
