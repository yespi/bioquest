# Academy — formato diapositiva (cursos)

> **Estado:** criterio editorial acordado (jun 2026). Cada **día = 1 diapositiva** mínimo: texto + gráfico explicativo.

## Principio

Cada página del curso debe funcionar como una **diapositiva de aula**:

1. **Concepto** en prosa clara (qué, por qué, para quién).
2. **Gráfico** que ilustre lo explicado (diagrama SVG integrado o enlace a Atlas).
3. **Ejemplos concretos** (ciencia ciudadana, especies, hábitats).
4. **Tarea opcional** de campo o exploración en Atlas/FaunaDex.

No es un listado de enlaces: el gráfico **acompaña** la explicación, no la sustituye.

## Ilustraciones raster (láminas)

Los diagramas SVG con **cajas/iconos** son útiles como fallback, pero el estándar preferido es una **lámina única** (acuarela/tinta estilo lámina de naturalista).

| type | Uso |
|------|-----|
| `illustration` | PNG/WebP en `public-pre/assets/edu/` — **preferido** para diagramas |
| `photo_collage` | Grid de fotos CC (iNaturalist) vía `collageId` en `edu-cc-photos.json` |
| `image` | Alias de `illustration` con `src` explícito |

### Collages CC (especies reales)

Para taxonomía (peces, invertebrados, plantas marinas, fauna terrestre) se usa un **collage** con varias fotos CC en la misma diapositiva. Las imágenes se obtienen de iNaturalist (licencias CC) y se sirven por el proxy existente `/ff-api/academy/photo-proxy/{id}/{size}` con atribución.

```json
"visual": {
  "type": "photo_collage",
  "collageId": "peces_costeros",
  "caption": "Peces costeros mediterráneos"
}
```

Catálogo: `public-pre/data/edu-cc-photos.json` (generado con `edu-build-cc-collages.mjs`).  
Asignación a slides: `edu-apply-collages-to-seed.mjs`.

**Generación:** Cursor Agent puede crear borradores con el generador de imágenes; revisar precisión científica antes de publicar. Alternativas: **iNaturalist/Wikimedia (CC)** — preferido para especies identificables; láminas MITECO/IEO, encargo a ilustrador.

## Tipos de visual SVG (fallback)

| type | Uso | Notas |
|------|-----|-------|
| `cc_examples` | CC marina | Preferir lámina cuando exista |
| `ocean_zones` | Zonas pelágicas | Sustituido por `illustration` id `ocean_zones` |
| `bentos_pelagic` | Bentos vs pelágico | Sustituido por lámina |
| `marine_factors` | Factores abióticos | SVG hub (pendiente lámina) |
| `atlas` | Reservado: miniatura / enlace mapa Atlas | CR, AMP, distribución mensual |

Campos en `edu-seed.json` por día:

```json
{
  "title": "Título de la diapositiva",
  "summary": "Una línea",
  "body": "Párrafos explicativos…",
  "bullets": ["Idea clave 1", "Idea clave 2"],
  "visual": { "type": "ocean_zones", "caption": "Leyenda bajo el gráfico" },
  "atlas_links": [{ "label": "…", "hash": "#atlas" }],
  "task": "Actividad breve"
}
```

## Guía editorial — cc-marina (borrador)

### Bloque 1 — Ciencia ciudadana (días 1–2)

- **Día 1:** Qué es la CC marina; miles de observadores complementan muestreos puntuales de investigadores.
- **Día 2:** Para qué sirve: validar modelos, detectar expansiones, informar políticas (AMP, especies invasoras).
- **Visual:** rejilla de ejemplos — fotografías en iNat, transectos intermareales, avistamiento invasoras, limpieza de playa, restauración de posidonia/marismas.

### Bloque 2 — El océano en capas (días 3–4)

- **Día 3:** Perfil océano vs profundidad (luz, presión, temperatura); qué especies habitan cada estrato.
- **Día 4:** **Bentos** (fondo: roca, arena, fango) vs **pelágico** (columna de agua); adaptaciones — p. ej. peces bentónicos aplanados ventralmente para apoyarse en el substrato; pelágicos fusiformes para nadar.

### Bloques siguientes (a redactar)

- Costas, AMP, cetáceos, CC aplicada, mini-proyecto… siempre con visual asociado.

## Integración Atlas

Los botones «Abrir Atlas» siguen abriendo mapas reales; **ESC** vuelve al día del curso (`academy-edu-return.js`).

## Referencias

- Seed: `public-pre/data/edu-seed.json`
- UI: `AcademyEduView.js` + `EduSlideVisual.js`
- [`PROPUESTA_ACADEMY.md`](../../_archive/2026-06-bioquest/PROPUESTA_ACADEMY.md)
