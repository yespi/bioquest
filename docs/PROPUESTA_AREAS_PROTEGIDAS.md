# Academy — Mapas de áreas protegidas y parques naturales

**Última actualización:** 2026-06-02  
**Estado:** Propuesta / diseño  
**Relacionado:** [ACADEMY_MAPAS_CIENTIFICOS.md](PROPUESTA_MAPAS_CIENTIFICOS.md) · [ACADEMY.md](../../_archive/2026-06-academy/ACADEMY_VISION_2026-05.md)

---

## Objetivo

Capa(s) de mapa en BioQuest Academy que muestren **espacios de conservación** (legales y científicos), con **ficha al clic** que incluya:

- Nombre, tipo, administración, superficie, año de designación
- Qué protege (especies, hábitats, ecosistemas)
- Por qué es especial (contexto ecológico)
- **Resumen IA** en tono documental (mismo estilo `_NARRATOR_STYLE` que fichas de especie)
- Enlaces a fuente oficial (MITECO, Protected Planet, IMMA e-Atlas…)

Si un solo mapa satura la UI o el rendimiento, **dividir en capas temáticas** activables desde el menú ⋮ o selector de vista.

---

## Tipos de área (concepto)

| Tipo | Naturaleza | Ejemplo |
|------|------------|---------|
| **Legal / gestionada** | Designación administrativa con normativa | Parque Nacional, Reserva Marina, LIC/ZEC Natura 2000, ZEPIM |
| **Científica / priorización** | Área identificada por expertos; no siempre es AMP | IMMA (cetáceos), ISRA (tiburones/rayas), EBSA |
| **Mixta** | Puede coincidir espacialmente | Reserva marina dentro de una IMMA |

En la ficha debe quedar claro: *«Área protegida legalmente»* vs *«Área de importancia científica (no implica protección legal)»*.

---

## Fuentes de datos

### Global (descarga periódica recomendada)

| Fuente | Qué cubre | Formato | Licencia / acceso | Notas |
|--------|-----------|---------|-------------------|-------|
| **[WDPCA / Protected Planet](https://www.protectedplanet.net/)** (ex WDPA+WD-OECM) | ~200k polígonos mundiales: terrestres, costeros y marinos | Shapefile, GDB, CSV; API v4 con `marine=true` | Uso no comercial OK; API key gratuita | Fuente **canónica global**. API v3 deprecada mayo 2026. Campo `designation` distingue tipos. |
| **[UNEP-WCMC MapServer](https://data-gis.unep-wcmc.org/)** | Capas WDPA marine/coastal, WDPCA tiles | WMS / FeatureServer / vector tiles | Atribución UNEP-WCMC | Útil para prototipo sin descargar todo el mundo. |
| **[Ramsar](https://www.ramsar.org/)** | Humedales de importancia internacional | Shapefile en [Ramsar Sites Information Service](https://rsis.ramsar.org/) | Libre con atribución | Muchos humedales costeros relevantes para aves/acuáticos. |
| **[IMMA e-Atlas](https://www.marinemammalhabitat.org/)** | Important Marine Mammal Areas (~280+ global) | GeoPackage/GeoJSON **bajo solicitud** (licencia no comercial) | CC BY-SA 4.0; contacto `imma.shapefile@imma-network.org` | **No son AMP legales.** Factsheets por área con especies y criterios. |
| **[ISRA e-Atlas](https://sharkrayareas.org/)** | Important Shark and Ray Areas (~800+ global) | Shapefile/KML/GeoJSON **bajo solicitud** (5–10 días) | Uso educativo; factsheets públicos | Complemento elasmobranquios. |
| **[EBSA](https://www.cbd.int/ebsa/)** (CBD) | Áreas ecológica/biológicamente significativas | Shapefile por región oceánica | Público CBD | Contexto marino amplio; menos granular que IMMA/ISRA. |
| **[OSPAR MPAs](https://www.ospar.org/work-areas/bdc/species-habitats/protected-areas)** | Atlántico nordeste | Descarga OSPAR | Libre | Relevante para Cantábrico/Atlántico español. |

### España (prioridad MVP — datos abiertos MITECO)

Fuente principal: **[Banco de Datos de la Naturaleza (MITECO / IEPNB)](https://www.miteco.gob.es/es/biodiversidad/servicios/banco-datos-naturaleza.html)**

| Capa MITECO | Contenido | Formato | Actualización |
|-------------|-----------|---------|---------------|
| **[ENP — Espacios Naturales Protegidos](https://www.miteco.gob.es/es/biodiversidad/servicios/banco-datos-naturaleza/informacion-disponible/enp.html)** | Parques Nacionales, Naturales, Reservas (marinas y terrestres), Paisajes, Monumentos… + tipologías autonómicas (~40+) | SHP + WMS | Dic 2025 |
| **[Red Natura 2000](https://www.miteco.gob.es/es/biodiversidad/servicios/banco-datos-naturaleza/informacion-disponible/rednatura_2000_desc.html)** | LIC, ZEC, ZEPA (marinas y terrestres) | SHP, GeoJSON, GML, KMZ | Continua |
| **[ZEPIM](https://www.miteco.gob.es/es/biodiversidad/servicios/banco-datos-naturaleza/informacion-disponible/zepim.html)** | Zonas protegidas Convenio de Barcelona (Mediterráneo) | SHP + WMS | Dic 2022 |
| **[Espacios marinos AGE](https://www.miteco.gob.es/es/biodiversidad/servicios/banco-datos-naturaleza/informacion-disponible/espacios_marinos_age.html)** | Competencia estatal, sin solapamientos | Derivado N2000+ENP+ZEPIM | Dic 2024 |

**Comunidades autónomas:** muchas capas están más actualizadas en portales autonómicos (Andalucía, Canarias, Baleares, País Vasco…). Fase 2: ingestión por CCAA vía APIs INSPIRE/WMS donde existan.

### Figuras españolas que el usuario espera ver

| Figura | Ámbito | Fuente |
|--------|--------|--------|
| Parque Nacional / Natural | Estatal + autonómico | ENP MITECO |
| Reserva Natural / Reserva Marina | Estatal + autonómico | ENP + espacios marinos AGE |
| LIC / ZEC / ZEPA | UE (Natura 2000) | Red Natura 2000 |
| ZEPIM | Mediterráneo internacional | MITECO ZEPIM |
| Área marina de especial interés (propuestas RD 531/2025…) | Levantino-balear | MITECO (enlace aparte) |

---

## Propuesta de capas en Academy (evitar un solo mapa saturado)

```
Menú ⋮ / selector de vista
├── 🏞️ Áreas protegidas (legal)     ← WDPA/ENP/Natura2000 filtrado por bbox
├── 🐋 Hábitats cetáceos (IMMA)       ← capa científica, color distinto
├── 🦈 Hábitats elasmobranquios (ISRA)
└── 🌿 Humedales Ramsar (opcional)
```

Alternativa UX: **un modo mapa «Conservación»** con toggles de subcapas (como hex/calor hoy).

**Código de colores sugerido:**

- Verde/azul semitransparente → protección legal
- Violeta → IMMA (cetáceos)
- Ámbar → ISRA (tiburones/rayas)
- Borde discontinuo → área científica sin estatus legal

---

## Arquitectura técnica

### Backend (`ecosistema-fauna`)

```sql
-- Esquema propuesto (PostGIS)
CREATE TABLE conservation_areas (
    id              SERIAL PRIMARY KEY,
    source          TEXT NOT NULL,      -- 'wdpa' | 'miteco_enp' | 'natura2000' | 'zepim' | 'imma' | 'isra' | 'ramsar'
    source_id       TEXT NOT NULL,      -- ID en fuente original (WDPA site_id, etc.)
    name            TEXT NOT NULL,
    name_es         TEXT,
    designation     TEXT,               -- tipo legal o categoría
    iucn_category   TEXT,               -- Ia, II, III… si aplica
    marine          BOOLEAN DEFAULT FALSE,
    country_iso3    TEXT,
    admin_level     TEXT,               -- 'estatal' | 'autonómico' | 'internacional'
    area_km2        DOUBLE PRECISION,
    established_year INTEGER,
    species_focus   TEXT[],             -- ['cetaceo','elasmobranquio','ave'] tags
    metadata        JSONB,              -- campos crudos de la fuente
    geom            GEOMETRY(MultiPolygon, 4326),
    updated_at      TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE (source, source_id)
);

CREATE INDEX idx_conservation_areas_geom ON conservation_areas USING GIST (geom);
CREATE INDEX idx_conservation_areas_source ON conservation_areas (source);
```

**Endpoints propuestos:**

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/academy/conservation-areas?swlat&swlng&nelat&nelng&layers=wdpa,imma` | GeoJSON FeatureCollection simplificado por bbox |
| GET | `/academy/conservation-areas/{id}` | Metadatos + enlace factsheet oficial |
| GET | `/academy/conservation-areas/{id}/sheet` | Ficha IA (cache `academy_ai_text`, kind=`conservation_area`) |

**Pipeline de ingestión** (`/mnt/scripts/bq_conservation_import.py`):

1. Descarga mensual WDPCA (solo `MARINE=1` + España `ISO3=ESP` para MVP).
2. Import MITECO ENP + Natura2000 + ZEPIM (GeoJSON/SHP).
3. IMMA/ISRA: import manual tras aprobar licencia (no redistribuir sin cumplir ToS).
4. Simplificación Douglas-Peucker por escala (`ST_Simplify`).
5. Deduplicar solapamientos → tabla `conservation_area_overlaps` opcional para UI.

### Frontend (`AcademyView.js`)

- Capa Leaflet `L.geoJSON` o vector tiles si >5k polígonos en bbox.
- Click polígono → panel lateral / visor con ficha (patrón `loadSheet`).
- Toggle subcapas en kebab; leyenda fija abajo del mapa.
- Cruzar con especie seleccionada: *«X observaciones de [especie] dentro de esta área»* (consulta espacial PostGIS).

### Ficha IA

Reutilizar patrón `species-rich` / `species-doc`:

1. **Entrada:** nombre, designation, species_focus, texto factsheet oficial (IMMA/ISRA/MITECO), Wikipedia si existe.
2. **Prompt:** explicar qué protege, por qué importa, especies emblemáticas, amenazas; tono Attenborough; **no inventar** designaciones legales.
3. **Cache:** `academy_ai_text` (`kind='conservation_area'`, `ref_key=source:source_id`, `prompt_ver=1`).
4. **Regeneración:** solo si cambia geometría o metadata (hash en `ref_key`).

---

## Volumen de datos y estrategia «mundial descargado»

| Dataset | Tamaño aprox. | Estrategia |
|---------|---------------|------------|
| WDPCA global | ~2–4 GB SHP comprimido | Descargar completo a `/mnt/data/conservation/wdpca/`; servir por bbox desde PostGIS |
| España ENP+N2000+ZEPIM | ~500 MB | MVP: solo España en PostGIS |
| IMMA global | ~50 MB GPKG | Tras licencia; ~280 polígonos |
| ISRA global | ~100 MB | Tras licencia; ~800 polígonos |

**No cargar el mundo entero en el cliente.** Patrón validado en Academy: fetch on pan-end + simplificación + cache Redis 15 min por tile/bbox.

---

## Roadmap propuesto

| Fase | Entrega | Esfuerzo |
|------|---------|----------|
| **0** | Este documento + solicitud licencias IMMA/ISRA | 1 día |
| **1a** | Import MITECO (ENP + Natura2000 marino) + mapa España + ficha básica (sin IA) | 3–4 días |
| **1b** | Ficha IA + cruce obs especie/área | 2 días |
| **2a** | WDPCA global en PostGIS + filtro bbox mundial | 2–3 días |
| **2b** | Capas IMMA + ISRA (tras licencia) | 2 días |
| **3** | Ramsar, EBSA, OSPAR; CCAA; vector tiles si rendimiento lo exige | 3–5 días |

**IDs backlog:** BQ-031 … BQ-034 (ver [academy_tareas_pendientes.md](../../TAREAS_PENDIENTES.md)).

---

## Riesgos y mitigaciones

| Riesgo | Mitigación |
|--------|------------|
| WDPA 200k polígonos lentos en Leaflet | PostGIS bbox + simplify; cluster en zoom bajo |
| IMMA/ISRA requieren solicitud manual | Pedir licencia educativa Yespi/BioQuest; mientras, solo ENP/Natura2000 |
| Solapamiento ENP ⊂ Natura2000 ⊂ WDPA | UI: «Contenida en: …»; no duplicar fichas |
| IA inventa protección legal | Prompt + disclaimer; mostrar siempre `designation` y `source` oficiales |
| ToS Protected Planet (no comercial) | BioQuest educativo encaja; documentar atribución en pie de mapa |

---

## Acciones inmediatas recomendadas

1. **Solicitar licencias** IMMA e ISRA (uso educativo, no comercial, atribución en app).
2. **Script de import** MITECO ENP + Natura2000 (GeoJSON) → PostGIS.
3. **Prototipo UI** en PRE: toggle «🏞️ Áreas protegidas» en kebab, sin IA, solo tooltip MITECO.
4. Registrar token Protected Planet API v4 en `(configure via environment)` si se usa API además de bulk download.

---

## Referencias

- Protected Planet API v4: https://api.protectedplanet.net/documentation  
- MITECO Banco de Datos de la Naturaleza: https://www.miteco.gob.es/es/biodiversidad/servicios/banco-datos-naturaleza.html  
- IMMA: https://www.marinemammalhabitat.org/  
- ISRA: https://sharkrayareas.org/  
- Ficha especie IA (patrón): `routes_academy.py` → `/species-rich`, `/species-doc`
