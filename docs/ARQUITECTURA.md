# BioQuest — Arquitectura del sistema

> **Cabecera reescrita el 2026-08-03** contra el código y los contenedores en marcha.
> Se corrigieron tres datos que estaban mal: la IP del servidor, el puerto de `bioquest-webapp`
> y la lista de ejes. La sección «Estructura de ficheros» de más abajo sigue describiendo el
> **legacy** de junio (archivado en `webapp/public-pre-legacy-20260602/`) y el fichero
> `REBUILD_PRE_ARQUITECTURA.md` al que remitía **no existe**; usar la tabla de esta cabecera.

## Visión general

BioQuest es la capa de gamificación y aprendizaje científico sobre FotoFauna.
Comparte usuarios, JWT, backend y base de datos con FotoFauna — de hecho el contenedor
`fauna_api` está definido en `./docker/ecosistema-fauna/docker-compose.yml` —
pero tiene su propio frontend Vue 3.

```
Internet → Cloudflare Tunnel → HanSolo (localhost)
  → bioquest-webapp (nginx:alpine, puerto 3012)
      ├── /pre/ → public-pre/  (desarrollo activo)
      └── /     → public/      (producción, solo con deploy explícito)
  → fauna_api (FastAPI, puerto 3005) — compartido con FotoFauna
  → postgres-global (PostgreSQL 16, red Docker yespi-net)
      └── DB: fauna
```

Operación (crons de Academy, QA, DNS): [`../../sistema/HANSOLO_CRON_Y_SERVICIOS.md`](../../sistema/HANSOLO_CRON_Y_SERVICIOS.md) ·
Textos IA en BBDD: [`ACADEMY_AI_TEXTOS_BBDD.md`](ACADEMY_AI_TEXTOS_BBDD.md)

## Vistas actuales (`webapp/public-pre/views/`)

| Vista | Eje |
|-------|-----|
| `AtlasView.js` | **Atlas** — mapas y gráficos por especie |
| `AcademyView.js` · `AcademyEduView.js` | **Academy** — fichas científicas y cursos |
| `AwardsView.js` | **Medallas / FaunaDex** |
| `ExploreView.js` · `BuceoView.js` | **Explore / Buceo** — dónde ir a observar |

**58 composables** en `public-pre/composables/`, agrupados por prefijo:
`useAcademy*` (11: charts, conservación, IUCN, invasoras, mapas, ranking, progreso educativo),
`bq-*` (16: analytics, consentimiento, meteo/satélite, viento, tesla-map, tema, escala),
`useEdu*` / `edu-*` (biblioteca y revisión de medios, novedades, filtros de películas),
y la base compartida (`useAuth`, `useAwards`, `useMap`, `useSpecies`, `useFilters`, `useBbox`,
`useFoto`, `useRarity`, `useLocation`).

## Rutas del backend (`ecosistema-fauna/backend/`)

15 ficheros `routes_*.py` sirven a FotoFauna y BioQuest a la vez:

`routes_academy.py` · `routes_academy_edu.py` · `routes_awards.py` · `routes_badges.py` ·
`routes_trophies.py` · `routes_pokedex.py` · `routes_buceo.py` · `routes_illustrations.py` ·
`routes_minka.py` · `routes_minka_autoid.py` · `routes_inat.py` · `routes_search.py` ·
`routes_scheduler.py` · `routes_sync.py` · `routes_files.py`

## Directorios de trabajo en `webapp/`

| Directorio | Qué es |
|------------|--------|
| `public-pre/` · `public/` | PRE (desarrollo) y PRO (producción) |
| `public-pre-legacy-20260602/` | Frontend anterior a la reescritura de junio |
| `public-pre-rebuild-20260603-aborted/` | Intento de rebuild **abortado** — candidato a borrar |
| `migration-pack-naming/` · `migration-pack-analytics/` | Paquetes de migración; el de naming ya está aplicado |
| `nginx-cache/` · `_backups/` | Caché y copias |

## Stack tecnológico

- **Frontend**: Vue 3 ESM CDN (sin build), módulos ES nativos
- **Backend**: FastAPI (Python), compartido con FotoFauna (`fauna_api`)
- **BD**: PostgreSQL 16 (`fauna` DB en `postgres-global` container)
- **Auth**: JWT compartido con FotoFauna (`FAUNA_JWT_SECRET`)
- **CSS**: Variables CSS custom (`--surf`, `--accent`, `--border`…), dark-first

## Estructura de ficheros

```
./docker/bioquest/webapp/public-pre/
├── index.html              ← App shell: nav, routing por hash, login, admin
├── composables/
│   ├── useAuth.js          ← JWT, login, /auth/me
│   ├── useAwards.js        ← Medallas, HUD badge, evaluateBadges
│   ├── useSpecies.js       ← Listado de especies por zona/bbox
│   ├── useMap.js           ← Leaflet, clusters, POIs de medallas
│   ├── useFoto.js          ← Fotos de especie via /pokedex/photos
│   ├── useRarity.js        ← Color por rareza de especie
│   ├── useFilters.js       ← Filtros compartidos: mes, estación, año, taxa
│   ├── useBbox.js          ← Bounding box del mapa
│   └── useLocation.js      ← Geocodificación, geolocalización
├── views/
│   ├── ExploreView.js      ← FaunaDex: mapa Leaflet + lista de especies
│   └── AcademyView.js      ← Fichas científicas, stats, gráficos por especie
└── img/
    ├── bq-logo-nav.jpg     ← Logo recortado para la barra de nav (92px alto)
    ├── bq-logo-grande.jpg  ← Logo grande para overlay hover
    ├── bq-logo.svg         ← Logo SVG helix ADN (legacy, no usado en nav)
    └── bq-tardigrade.svg   ← Tardígrado SVG (legacy)
```

## Tabs y routing

> **Naming acordado:** [`NAMING_EJES.md`](NAMING_EJES.md) — **3 pestañas**; Medallas dentro de FaunaDex.

Hash routing objetivo:

| Tab | Hash | Login | Estado PRE hoy |
|-----|------|-------|----------------|
| **Atlas** | `#atlas` | No | 🔄 Hoy `#academy` + `AcademyView.js` |
| **Academy** | `#academy` | Parcial (cursos) | 📋 Propuesta — [`PROPUESTA_ACADEMY.md`](../../_archive/2026-06-bioquest/PROPUESTA_ACADEMY.md) |
| **FaunaDex** | `#explore` | Sí + Minka | ✅ `ExploreView.js` |
| ~~Medallas~~ | ~~`#awards`~~ | — | 🔄 Integrar en FaunaDex → `#explore/medals` |

**Redirects legacy:** `#academy` (mapas, pre-migración) → `#atlas` · `#awards` → `#explore/medals`

```
./docker/bioquest/webapp/public-pre/views/
├── AtlasView.js      ← (futuro) mapas/gráficos; hoy AcademyView.js
├── AcademyView.js    ← (futuro) formación; hoy = mapas hasta migración
├── ExploreView.js    ← FaunaDex + subvista Medallas
└── AwardsView.js     ← absorber dentro de ExploreView
```

## Filtros compartidos

Los filtros viven en `useFilters` instanciado en `index.html` y pasados a
`ExploreView` como props (`sharedFilters`, `sharedBbox`, `sharedLocation`).
Un `watch()` en ExploreView recarga las especies cuando cambian los filtros.

Filtros disponibles:
- **Mes** (`filterMonth`)
- **Estación** (`filterSeason`) — mutuamente excluyente con mes
- **Año desde/hasta** (`filterYearFrom`, `filterYearTo`)
- **Grupos taxonómicos** (`filterTaxa`) — multiselect, enviado como `iconic` a Minka

## Atlas — datos (API `/academy/*`)

> UI futura: pestaña **Atlas** (`#atlas`). Backend conserva prefijo `/academy/` sin breaking change.

### Clave dual iNat / Minka (importante)

| Concepto | Valor |
|----------|--------|
| **Clave canónica** | `inat_id` (iNaturalist) — catálogo, `public_observations.taxon_id`, Academy |
| **ID Minka** | Distinto por plataforma → `public_observations.minka_taxon_id` + `academy_catalog_meta.minka_taxon_id` |
| **Descarga iNat** | `bq_download_inat.py` — bbox Europa o mundial según grupo/CR |
| **Descarga Minka** | `bq_download_minka.py` — mismo catálogo por `taxon_name`, **bbox Europa** (España incluida) |
| **Sync nocturno** | `bq_sync_nightly.py` — alterna iNat y Minka hasta 5000 obs/plataforma/especie |
| **Migraciones ID** | `TAXON_ID_MIGRATIONS` en `academy_catalog.py` — corrige IDs Minka usados por error como iNat |

En España ~**32 %** de las obs locales en bbox peninsular son Minka (jun 2026). Para FaunaDex y filtros «mis observaciones» hay que cruzar **ambos** IDs (`routes_pokedex.py`).

Las especies icónicas se descargan de **iNat y Minka** y se almacenan en `public_observations`:

```sql
CREATE TABLE public_observations (
  id            BIGSERIAL PRIMARY KEY,
  taxon_id      INT,          -- iNaturalist taxon ID (clave de referencia)
  taxon_name    TEXT,
  obs_id        BIGINT UNIQUE,-- iNat: 9e9+id; Minka: id nativo
  lat           FLOAT,
  lng           FLOAT,
  obs_year      SMALLINT,
  obs_month     SMALLINT,
  obs_hour      SMALLINT,     -- iNat time_observed_at cuando existe
  quality_grade TEXT,
  source        TEXT NOT NULL DEFAULT 'minka',  -- 'inat' | 'minka'
  minka_taxon_id INT,         -- ID taxon Minka (≠ taxon_id)
  created_at    TIMESTAMPTZ
);
```

Scripts (contenedor `fauna_api`):
```bash
# Sync dual nocturno (cron recomendado)
python3 /app/bq_sync_nightly.py

# Manual
python3 /app/bq_download_inat.py --all
python3 /app/bq_download_minka.py --all
python3 /app/bq_backfill_catalog_meta.py   # family + minka_taxon_id
```

Legacy solo-Minka: `bq_download_observations.py --all` (sin bbox; preferir `bq_download_minka.py`).

## Endpoints Academy

| Endpoint | Descripción |
|----------|-------------|
| `GET /academy/target-species` | Catálogo especies objetivo + nº obs locales |
| `GET /academy/species-stats?taxon_inat_id=X` | Stats: total, años, distribución mensual |
| `GET /academy/heatmap?taxon_inat_id=X&month=M` | Puntos [lat,lng,count] para mapa |
| `GET /academy/year-trend?taxon_inat_id=X` | Tendencia anual desde 2010 |
| `GET /academy/research-vessels?swlat&swlng&nelat&nelng` | Buques investigación/ONG en bbox |
| `GET /academy/conservation-areas` | Polígonos WDPA España |
| `GET /academy/threatened-marine` | Mapa especies MITECO marinas |
| `GET /academy/critically-endangered` | Mapa especies CR |

Todos los endpoints de stats/heatmap tienen fallback a Minka live si no hay datos locales (pendiente alinear post-limpieza Minka — ver [`PLAN_OPTIMIZACION_2026-06-07.md`](../../_archive/2026-06-bioquest/PLAN_OPTIMIZACION_2026-06-07.md)).

## Buques ecológicos (mapa Atlas)

Catálogo semilla: `ecosistema-fauna/backend/data/research_vessels_seed.json`  
Sync AIS: `bq_research_vessels_sync.py` → `research_vessel_positions`  
Clasificación UI: `conservation_layer_class.classify_vessel_category()`

| Capa (objetivo) | Color | Estado jun 2026 |
|-----------------|-------|-----------------|
| `vessels_es` — instituciones españolas | Azul | Pendiente (hoy mezclado en `vessels_ocean`) |
| `vessels_ngo` — ONG | Naranja `#f97316` | ✅ |
| `vessels_foreign` — investigación internacional | Morado | Pendiente |
| `vessels_other` — auxiliar / ciudadana | Gris | Pendiente |

Documentación ampliada: [`BUQUES_ECOLOGICOS.md`](BUQUES_ECOLOGICOS.md).

## Trabajo en background (sin impacto web)

Orquestadores en `/mnt/scripts/fauna/` — ver [`HANSOLO_CRON_Y_SERVICIOS.md`](/mnt/docs/sistema/HANSOLO_CRON_Y_SERVICIOS.md).

| Pipeline | Propósito |
|----------|-----------|
| `bq-academy-daily.sh` | Buques → sync obs → precache → IUCN |
| `bq-academy-weekly.sh` | WDPA, invasoras, warm IA |
| `bq-ai-warm.py` | species_doc, species_rich, chart_help |
| `bq_sync_nightly.py` | Rotación descarga iNat+Minka por especie |

## Academy educativa (roadmap)

Cursos IA, documentales, organizaciones. Propuesta: [`PROPUESTA_ACADEMY.md`](../../_archive/2026-06-bioquest/PROPUESTA_ACADEMY.md).  
Endpoints futuros: `/academy/edu/*` o `routes_academy_edu.py`.

## Integración FotoFauna ↔ BioQuest

- **SSO**: cookie `yespi_access` compartida en `.yespi.es`; el JWT se acepta en ambas apps
- **Botón BioQuest en FotoFauna**: en `index-mobile.html`, header junto al avatar
- **Botón FotoFauna en BioQuest**: en nav de `index.html`
- **Botón BioQuest en menú usuario FotoFauna**: enlace amarillo `🧬 BioQuest ↗`

## Admin panel

El panel de administración se abre como drawer lateral (iframe de `/admin/`).
Solo visible para usuarios con rol `admin` (`user.is_admin`).

## Despliegue

- **PRE**: `./docker/bioquest/webapp/public-pre/` — editar siempre aquí
- **PRO**: `./docker/bioquest/webapp/public/` — copiar solo con permiso explícito
- Reiniciar frontend: `docker restart bioquest-webapp`
- El backend se reinicia con: `docker restart fauna_api`
