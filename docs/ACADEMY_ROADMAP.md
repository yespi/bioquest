# BioQuest Academy — Hoja de ruta (jun 2026)

> Build actual PRE: `bq055academy1`  
> Entorno: solo PRE (`./docker/bioquest/webapp/public-pre/`)

## Visión

Academy como **hub de formación y divulgación**: cursos tipo manual de buceo (láminas + zoom), biblioteca de documentales con recomendador, organizaciones de referencia, voluntariado en la costa catalana y, en fases posteriores, asistente IA con animaciones.

---

## Fase A — Infraestructura y UX (en curso)

| # | Entrega | Estado |
|---|---------|--------|
| A1 | Fix «Failed to fetch» — `waitForFfApi` + `fetchJsonQuiet` + reintentar | ✅ `bq055academy1` |
| A2 | Tema oscuro catálogo Academy | ✅ `bq-edu-hub.css` |
| A3 | Hub documentales — Cousteau primero, embeds, búsqueda, suscripciones, progreso local | ✅ `EduMediaHub` |
| A4 | Organizaciones priorizadas (Minka-SDG, VIMAR, iNat, OPK, ODM, CRIS) | ✅ `EduOrgsHub` |
| A5 | Sección Voluntariado (Minka/Aneris, BioMarató, limpiezas, murciélagos…) | ✅ `EduVolunteerHub` |
| A6 | Botón flotante «Cursos», flechas ←→ página, ↑↓ scroll texto | ✅ |
| A7 | ESC en lightbox: primero reset zoom, luego cerrar | ✅ |
| A8 | Datos en `edu-media-library.json`, `edu-orgs.json`, `edu-volunteer.json` | ✅ |

---

## Fase B — Cursos (siguiente)

| # | Entrega | Estado |
|---|---------|--------|
| B1 | Desplegar `./assets/edu/` PNG en color (referenciados en seed) | ⏳ |
| B2 | Todos los cursos tema oscuro unificado en visor | ✅ visor; catálogo ✅ |
| B3 | Textos extensos + tips/anécdotas en guiones (`edu-scripts` → seed) | 🔄 parcial |
| B4 | Cursos `aves` y `nudibranquios-cat` contenido completo | ⏳ |
| B5 | Export local por curso (HTML + imágenes; videos como enlaces) | ⏳ |
| B6 | Incrustar videos YouTube en diapositivas cuando aplique | ⏳ |

---

## Fase C — IA y enriquecimiento

| # | Entrega | Estado |
|---|---------|--------|
| C1 | Asistente virtual por curso (TTS + explicación conceptos) | ⏳ |
| C2 | Animaciones SVG / secuencias para conceptos clave | ⏳ |
| C3 | Enlaces externos contextuales en cada diapositiva | 🔄 parcial |
| C4 | Sincronizar progreso guest → nube al login | ⏳ |

---

## Fase D — Ideas añadidas (no pedidas explícitamente)

- **Calendario voluntariado** — iCal de salidas Minka/FECAS y eventos BioMarató
- **Mapa de puntos de muestreo** — enlace Atlas ↔ curso CC marina
- **Listas de reproducción por tema** — «Primeros auxilios marino», «Fotografía macro»
- **Modo offline PWA** — cursos descargados + reproductor documentales en caché
- **Certificados con QR** — verificación pública del diploma
- **Ruta `#academy/media`** — deep links por sección
- **Valoración ★** por documental (local) para mejorar recomendador
- **Panel docente** — notas privadas por diapositiva

---

## Datos y archivos clave

| Recurso | Ruta |
|---------|------|
| Media library | `public-pre/data/edu-media-library.json` |
| Organizaciones | `public-pre/data/edu-orgs.json` |
| Voluntariado | `public-pre/data/edu-volunteer.json` |
| Seed cursos | `public-pre/data/edu-seed.json` + `backend/data/edu-seed.json` |
| Patch merge | `/mnt/scripts/fauna/edu-patch-academy-seed-media-orgs.mjs` |
| Progreso media | `localStorage` `bq_edu_media_subs_v1`, `bq_edu_media_watch_v1` |

---

## Validación PRE

```bash
# Abrir
https://bioquest.yespi.es/pre/#academy

# API slides
curl -s "https://bioquest.yespi.es/ff-api/academy/edu/courses/cc-marina/day/1/slide/1" | head -c 200
```
