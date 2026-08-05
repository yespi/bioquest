# BioQuest — Naming de ejes (decidido)

> **Estado:** ✅ acordado 7-jun 2026 · **Swap:** [`MIGRATION_PACK.md`](../../_archive/2026-06-bioquest/MIGRATION_PACK.md)  
> **Nav objetivo:** tres pestañas principales — **Atlas**, **Academy**, **FaunaDex**.

---

## Modelo final

| Nav (usuario) | Hash | Contenido | Código hoy (PRE) |
|---------------|------|-----------|------------------|
| **Atlas** | `#atlas` | Mapas, gráficos, heatmaps, conservación, buques, MITECO/CR, fichas por especie | `#academy` · `AcademyView.js` |
| **Academy** | `#academy` | Cursos IA, documentales, organizaciones, voluntariado | *(nuevo)* · ver [`PROPUESTA_ACADEMY.md`](../../_archive/2026-06-bioquest/PROPUESTA_ACADEMY.md) |
| **FaunaDex** | `#explore` | Mapa, especies por zona, colección **+ medallas** (subsección) | `#explore` + `#awards` separado hoy |

**Medallas:** no es pestaña propia. Pasa a ser **subsección de FaunaDex** (pestaña interna, drawer o `#explore/medals`).  
Hash legacy `#awards` → redirect a `#explore/medals`.

---

## Nav objetivo (wireframe)

```
BioQuest
├── 🗺️ Atlas      #atlas     — Explora datos
├── 🎓 Academy    #academy   — Aprende (cursos, vídeos, orgs)
└── 📖 FaunaDex   #explore   — Colecciona
         └── 🏅 Medallas     #explore/medals  (dentro, no en barra principal)
```

---

## Migración técnica (fases)

### Fase 1 — Marca y redirects (bajo riesgo)

| Cambio | Detalle |
|--------|---------|
| Nav | 3 tabs: Atlas · Academy · FaunaDex |
| Redirect | `#academy` (viejo mapas) → `#atlas`; `#awards` → `#explore/medals` |
| Enlaces | `?taxon=` y `#academy?taxon=` → `#atlas?taxon=` |
| Copy UI | Textos «Academy es público» → «Atlas es público» donde aplique |

### Fase 2 — FaunaDex + medallas

| Cambio | Detalle |
|--------|---------|
| Integrar `AwardsView` | Sub-vista dentro de `ExploreView` o router hash anidado |
| Quitar tab Medallas | Eliminar entrada `{ id: 'awards' }` del nav |
| HUD medallas | Mantener badge en nav FaunaDex |

### Fase 3 — Academy nueva sección

| Cambio | Detalle |
|--------|---------|
| `AcademyView.js` (nuevo) | Landing cursos/documentales/orgs |
| Renombrar vista mapas | `AcademyView.js` → `AtlasView.js` (opcional, cosmético) |
| API | Nuevos `/academy/*` educativos; **mantener** `/academy/*` datos como alias Atlas API (sin breaking) |

### Sin renombrar (alias internos OK)

- Rutas API datos: `/academy/target-species`, etc.
- Tablas BD: `academy_taxon_cache`, `academy_ai_text`
- Crons: `bq-academy-daily.sh`
- Docs: «**Atlas** (UI mapas) · **Academy API** (backend datos) · **Academy** (UI formación)»

---

## Glosario

| Término usuario | Término técnico legacy |
|-----------------|------------------------|
| Atlas | `AcademyView`, `#academy` (hasta redirect) |
| Academy (nuevo) | `routes_academy_edu` (futuro), `#academy` (nuevo sentido tras migración) |
| FaunaDex / Medallas | `ExploreView`, `AwardsView`, `/awards/*` |

> **Atención:** tras la migración, `#academy` significará formación. El redirect `#academy`→`#atlas` solo aplica a **enlaces antiguos** guardados antes del cambio; la implementación debe usar un flag o fecha de corte, o rutas distintas (`#atlas` vs `#academy` sin conflicto).  
> **Orden recomendado:** primero introducir `#atlas` + redirect desde viejo `#academy`; luego lanzar nueva Academy en `#academy` cuando la UI educativa exista.

---

## Documentos relacionados

- [`PROPUESTA_ACADEMY.md`](../../_archive/2026-06-bioquest/PROPUESTA_ACADEMY.md) — sección educativa
- [`ARQUITECTURA.md`](ARQUITECTURA.md) — routing y stack
- [`PROPUESTA_BIOHUB.md`](../../_archive/2026-06-bioquest/PROPUESTA_BIOHUB.md) — alias obsoleto → ver PROPUESTA_ACADEMY
