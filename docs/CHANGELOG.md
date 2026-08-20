# BioQuest — Changelog

## 2026-08-20 — Explore/Buceo: iconos de predicción meteo WMO + arreglos satélite
**Build PRO:** `bq5b4dfbc3` · **Git:** `2046c5e37` + `d6c7cce95` + `b34a7b3ac` + `c95a3289d` (`hansolo-dockers`) · **Deploy:** `deploy-to-pro.sh` + `docker restart fauna_api`

- **Iconos de predicción meteorológica (WMO) sobre el mapa Buceo**, visibles en todas las capas, con **LOD por zoom**:
  - **España (z<7):** 52 iconos — uno por provincia (centroide del geojson `spain-provinces.geojson`), tamaño compacto (22px), sin nombre.
  - **Región (z7-9):** + ciudades grandes, tamaño 34px, con nombre.
  - **Detalle (z≥9):** + poblaciones menores, 44px, con nombre.
- **Backend:** nuevo endpoint `GET /buceo/forecast-batch?coords=lat,lng|lat,lng` en `routes_buceo.py` (lo sirve `fauna_api` :3005): una sola llamada Open-Meteo multi-coord (máx 100 coords, caché 30 min, cooldown 429) → `weather_code` (WMO) diario a 7 días por punto.
- **Eficiencia:** el frontend parte en lotes ≤90 coords y fusiona → todos los puntos reciben datos (antes se cortaban a 60).
- **Declutter por distancia en píxeles (≥100px):** las 52 provincias siempre visibles; las ciudades se espacian priorizando las más pobladas → representación equitativa del territorio (zonas densas dejan de apiñarse, zonas rurales conservan su provincia).
- **Dedupe por nombre:** máximo 1 icono por sitio (6 ciudades coincidían con provincia homónima: Barcelona, Girona, Huesca, Lleida, Tarragona, Zaragoza → ahora gana la ciudad con tier `prov`).
- **Refresco al cambiar de día:** `setDay()` en `selectForecastDay`/`selectForecastTR` → los emojis cambian al seleccionar otro día en la barra `bq-buceo__fc-days`.
- **Arreglo satélite blanco:** `makeWmsLayer`/`makeDwdLayer` pasan a `format: 'image/png'` (JPEG no soporta alfa → fondo blanco puro) + sonda `isEumetsatFrameEmpty` (GetMap 64×64, <500B = vacío) que conmuta a `rgb_geocolour` (IR nocturno) + re-evaluación día/noche cada 20 min.
- **Capa:** nuevo composable `composables/bq-fc-icons.js` (`createBqFcIcons`); CSS `.bq-fc-icon*` en `index.html`.
- **Verificado con Playwright (PRO):** z5=52 provincias, z8=56 iconos ≥105px, z10=84 ≥102px, 0 duplicados, cambio de día OK.


## 2026-08-15 — Academy: cascada IA saneada (Gemini fuera, OpenRouter arreglado)
**Deploy:** `docker restart fauna_api` (BioQuest no tiene backend propio, lo sirve `fauna_api`)

El backend de Academy (`routes_academy.py`, resumen narrado de especies + ficha rica de especie)
tenía `OPENROUTER_TEXT_MODEL` apuntando a un modelo retirado del catálogo
(`meta-llama/llama-3.3-70b-instruct:free`) → corregido a `nvidia/nemotron-3-super-120b-a12b:free`.
**Gemini eliminado del todo** (función `_ai_call_gemini`, con reintentos que llegaban a
desperdiciar 30s por llamada contra una cuota gratuita de Google ya agotada — 20 peticiones/día
por proyecto/modelo, compartida con el resto de apps del host). Ahora la cascada va directa a
OpenRouter → Groq.


## 2026-08-01 17:16 — BioQuest
**Build PRE:** `bq71724b3a` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-30 — Explore: 4 capas atmósfera + SEO admin
**Build PRO:** `bqc7fdadc4` · **Git:** `d0a2b325` (`hansolo-dockers`)

- Capas exclusivas en Explore: **Meteo** (nubes animadas, default), **Temperatura** (azul→rojo), **Viento**, **Satélite** (Esri).
- API `meteo-grid` + campo `cloud` (Open-Meteo `cloud_cover`); composable `bq-meteo-tint.js`.
- Informe SEO solo visible/usable por `contact@yespi.es`.
- Doc: [`EXPLORE_CAPAS.md`](EXPLORE_CAPAS.md), actualización [`BUCEO_DISENO.md`](BUCEO_DISENO.md) §6.


## 2026-07-16 06:57 — BioQuest
**Build PRE:** `bqd783f134` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-14 05:40 — BioQuest
**Build PRE:** `bq22d5c205` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-06 05:54 — BioQuest
**Build PRE:** `bqaf7f1583` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-06 05:49 — BioQuest
**Build PRE:** `bq5e6b2317` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-05 21:31 — BioQuest
**Build PRE:** `bqe8c6384f` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-05 19:01 — BioQuest
**Build PRE:** `bqccdd0a21` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-04 10:05 — Explore/Buceo UX móvil + escritorio + viento → PRO

**Build PRO:** `bq48be8d62` · **PRE:** `bqa202607041000` · **Gates:** OK

### Explore — móvil
- **Mapa visible**: split pane mapa↔ficha (66%/34%) con peek táctil.
- **Scroll unificado**: previsión 7 días + ficha en un solo bloque (sin `sticky` ni doble scroll).
- **satModal**: `Teleport` a `body`, `z-index` 10250, botones cerrar 44px + pie «Cerrar vista satélite».

### Explore — escritorio
- **Previsión 7 días**: grid 7 columnas sin scroll horizontal; ficha lateral 360px.
- **Scroll ficha**: previsión y detalle desplazan juntos.

### Explore — viento
- **Partículas**: longitud ÷3; grosor ×1.75 y mayor luminosidad (opacidad 68–96%, estelas más persistentes).

### Archivos clave
- `webapp/public/views/BuceoView.js`
- `webapp/public/composables/bq-wind-particles.js`
- `webapp/public/index.html`

---

## 2026-07-04 09:30 — Explore/Buceo sprint UX + rendimiento → PRO

**Build PRO:** `bqa54135b7` · **PRE:** `bqa202607040926` · **Gates:** OK · **`fauna_api` restart**

### Explore — funcionalidad
- **Buscador POI-first**: `GET /buceo/search` + híbrido Nominatim (centros, pecios, calas antes que geocoding genérico).
- **Webcams**: catálogo completo `?all=1`, filtro viewport, preview JPG vía proxy (`/buceo/webcam-preview`), capa siempre visible (sin toggle).
- **Barra 7 días**: carga paralela detalle+meteo+forecast; un solo pintado (`sidePanelReady`); cambio de día recarga viento+idoneidad con hint «recalculando…».
- **Capas fijas**: centros, pecios e inmersiones por LOD/zoom; eliminados toggles redundantes en leyenda.

### Explore — rendimiento mapa
- **Prefetch viento** al montar (Cataluña z8) → pintado en cuanto Leaflet está listo (~1–2 s).
- **Viento e idoneidad desacoplados**: el viento ya no espera lotes `weather-batch` (antes >10 s pantalla vacía).
- **`_scheduleViewportSync`**: un solo debounce 300 ms tras pan/zoom (elimina triple redibujado: LOD + viento + idoneidad por separado).
- **`POST /buceo/weather-batch`**: chunks JSON (máx. 25 coords); mar/tierra en paralelo.
- **Watchdog viento** (2,8 s) solo si la capa queda inactiva tras error.
- **Descartado** velo «estilo videojuego» (bloqueaba 10 s esperando teselas+idoneidad+POIs).

### Backend (`routes_buceo.py`, `poi_cache.py`)
- Endpoints: `search`, `webcam-preview`, `webcams?all=1`, `weather-batch` POST.
- `wind-arrows`: expande bbox pequeño (evita 422).

### Archivos clave
- `webapp/public/views/BuceoView.js`
- `webapp/public/composables/bq-wind-particles.js`, `bq-api-fetch.js`
- `ecosistema-fauna/backend/routes_buceo.py`

---

## 2026-07-04 01:15 — Explore/Buceo → PRO (viento Windy + satélite + webcams)

**Build PRO:** `bq2a05720e` · **Origen:** deploy PRE→PRO tras validación mapa (01:13)

### Explore — mapa
- **Viento estilo Windy**: capa de color (gradiente km/h) + partículas blancas; trazo **proporcional a velocidad local** (longitud, grosor, opacidad).
- **Previsión**: iconos WMO + modo **TR** por defecto; al elegir día recarga viento e idoneidad de ese día.
- **Rejilla viento densa** en API (`routes_buceo.py`, caché `:g2`) — ~48–120 puntos según zoom.
- **Modal satélite**: imagen Esri + viento (solo flechas, sin tinte) + POIs + etiquetas población.
- **Etiquetas población**: texto **oscuro con halo blanco** en mapa principal; blanco+sombra negra en satélite (`bq-city-labels.js`).
- **Webcams**: capa activa por defecto; modal con **fotograma JPG Skyline** (iframe bloqueado por X-Frame-Options) + enlace vídeo en vivo; recarga al pan del mapa.
- **Tooltips POIs**: fondo negro opaco por encima del viento.

### Deploy
- `deploy-to-pro.sh` + regression gates OK.
- PRE y PRO sincronizados (`public-pre` / `public`).

---

## 2026-07-02 20:53 — BioQuest
**Build PRE:** `bq8be0b7b2` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-02 20:08 — BioQuest
**Build PRE:** `bqa9e0cc97` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-02 19:19 — BioQuest
**Build PRE:** `bqb08991d8` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-07-01 — Ayuda in-app actualizada (PRE)
- **Menú ❓ Cómo funciona BioQuest**: ampliado de 3 a **5 secciones** — Atlas (mapa oscuro), Academy (voluntariado por defecto), FaunaDex (reto semanal), **Explora/buceo** (capas mar-tierra, satélite móvil) y **Buscador ⌘K**.
- Subtítulo corregido: «Cuatro modos» + Explora como cuarta pestaña principal.
- Documentación canónica en `/mnt/docs/bioquest/CHANGELOG.md` y tareas en `TAREAS_PENDIENTES.md`.

## 2026-06-25 (mañana) — Motores IA: Groq Scout deprecado + fichas IA automáticas + YOLO config

**Groq deprecia Llama 4 Scout 17B** (único modelo de visión de GroqCloud), decomiso **2026-07-17**. FF lo usaba como capa 3 de 4 en visión (`vision_identify`/`vision_detect`). **Migración robusta**: guard `_groq_vision_available()` que desactiva la capa sola tras esa fecha → el pipeline cae a iNat CV (capa 1, principal) + Gemini (capa 2) + OpenRouter (capa 4) sin romperse. Configurable por env `GROQ_VISION_MODEL`. El `/vision/health` reporta `groq_vision: {available, model, retire_date}`.

**OpenRouter activado** (la clave no llegaba al contenedor → capa 4 de visión y fallback de fichas estaban inactivos). Añadida `OPENROUTER_API_KEY` al `.env` del proyecto (gitignored). Modelo de texto configurable `OPENROUTER_TEXT_MODEL` (default `meta-llama/llama-3.3-70b-instruct:free`; el antiguo `gemini-2.0-flash-001` daba 404).

**AI-FICHAS-AUTO**: las fichas IA de especie (`species_doc`/`species_rich`) solo se generaban **bajo demanda** → las especies nuevas (insectos, aves CR) quedaban sin ficha rica hasta que alguien las visitaba. Nuevo `academy/prefill_species_docs.py` (batch idempotente) **integrado en el academy_sync nocturno** → cada noche pre-genera las fichas que falten. 14 fichas detectadas faltantes (insectos+CR nuevos); se generan cuando los tiers gratuitos de IA liberan rate-limit.

**YOLO configurable**: modelo de detección parametrizado por env `YOLO_MODEL` (default `yolo11n`; permite subir a `yolo11s`/`yolo11m` para más precisión sin tocar código). Era `yolo11n.pt` hardcoded.

## 2026-06-25 (madrugada) — Buscador: gráficas Atlas + barcos por nombre + analytics
- **Visualizaciones del Atlas indexadas** (`_VIZ`): "rarefacción", "co-ocurrencia", "ranking", "IUCN/amenazadas", "invasoras", "mapa de calor", "latitudinal", "fenología"… abren su vista del Atlas. Atrae a científicos que buscan estos nombres técnicos.
- **Barcos por nombre legible**: "margalef"→*Ramón Margalef*, "sarmiento"→*Sarmiento* (antes mostraba el código MMSI). `_vessel_result` extrae el nombre propio del `about`. **Bug encontrado y resuelto**: faltaba `import re` → el `except` se tragaba el `NameError` y devolvía 0 barcos.
- **Analytics del buscador** (`ecosistema-search.js?v=ecosrch3`): eventos GA4 `search` (término + nº resultados) y `search_select` (tipo de resultado elegido). Mide adopción del buscador recién lanzado y detecta términos sin resultados. Respeta Consent Mode v2 (no-op sin consentimiento). FF+BQ, PRE+PRO.
- **Caché Academy recomputada**: las especies nuevas (CR + insectos) ya tienen stats; 208 precalculadas, 25 omitidas (especies CR exóticas sin obs en Minka, p.ej. lémures de Madagascar — esperado).
- **Grupos `anfibio`/`reptil`** añadidos a `GROUP_LABELS` y `REALM_LAND_GROUPS` del backend (eliminado warning "grupo desconocido" del tritón del Montseny y otros anfibios).
- **Infra deploy**: documentado y resuelto el bug recurrente del `git pull` bloqueado en HanSolo por los artefactos SEO que genera el cron (push OK pero código no llegaba a PRO).

## 2026-06-24 (noche) — Buscador ⌘K a PRO + mapa de navegación + fix login móvil
- **SEARCH-N1 promovido a PRO**: widget command-palette ⌘K (botón 🔍 nav BQ, FAB FF) + endpoint `/search`. Funcional en producción.
- **Cobertura ampliada** del buscador (`routes_search.py`): (1) términos genéricos ES por grupo — "mariposa", "tiburón", "abeja", "delfín" encuentran especies aunque el `common` esté en inglés; (2) **funcionalidades/manual** — "recortar", "editar", "identificar", "subir a Minka", "publicar", "filtrar", "mejorar contraste"… llevan a la página/acción; (3) **secciones de navegación** — Atlas, Academy, FaunaDex y sub-secciones edu (Voluntariado, Organizaciones, Artículos, Documentales, Películas, Cursos). El buscador es ahora un mapa para viajar por cualquier punto de ambas webs.
- **Fix login Google en móvil** (FotoFauna): MobileApp usaba popup (`window.open`), bloqueado en móviles → token no volvía. Ahora detecta móvil/táctil y usa **redirect en la misma pestaña** (como el escritorio y BQ). PRO.

## 2026-06-24 — Buscador universal ⌘K (SEARCH-N1, sin IA) (PRE)
- Nuevo **buscador command-palette** (icono 🔍 en la nav de BQ + atajo Ctrl/⌘K) que indexa el **ecosistema-fauna** (FF+BQ): especies, secciones, gráficas del Atlas, reservas, barcos y recursos/manuales. Resultados agrupados por tipo, navegación con teclado, insensible a acentos ("delfin"→"Delfín").
- Backend: endpoint común `/search` (`backend/routes_search.py`) sobre `academy_catalog_core` + `conservation_areas` + `research_vessel_profiles` + `academy_edu_sites`, con `unaccent`. Widget compartido `ecosistema-search.js` (vanilla, mismo fichero en FF y BQ).
- Es la **fase 1** del buscador; la capa IA conversacional (N2-N3: "luego te cuento" + toast + consultas tipo "dime dónde buceo hoy") queda para después de Buceo. Ver `TAREAS_PENDIENTES.md`.

## 2026-06-24 — Atlas: +48 especies (23 CR + 25 insectos) validadas por API (PRE)
- **ATLAS-9**: ampliado el catálogo Academy/Atlas con **23 especies CR nuevas** (CR total 54→77) y **25 insectos** (grupo `insecto`, 0→25). **Ningún ID inventado**: cada `inat_id` se resolvió y verificó contra la API real de iNaturalist (estatus IUCN = `CR` confirmado por *IUCN Red List* para las CR; `iconic_taxon_name = Insecta` para los insectos), y se mapeó su `minka_taxon_id` real contra Minka (34 con mapeo Minka → con observaciones en el Atlas).
- Descartadas en validación varias candidatas que la API NO confirmó como CR (p. ej. *Lynx pardinus*=VU, *Numenius tenuirostris*=EX) o que resolvían a un taxón distinto — justo el riesgo que evita validar contra la fuente real.
- Semilla reproducible en el repo: `backend/academy/seed_cr_extra.json`, `seed_insects.json` y `seed_extra_species.py` (idempotente, `python3 -m academy.seed_extra_species`). El núcleo se carga desde `academy_catalog_core` al arrancar el backend.

## 2026-06-24 — Atlas: navegación de gráficas y selector coherentes (PRE)
- **ATLAS-5 (la flecha no para)**: la navegación con flechas del visor (`presentGo`) ya **no hace wrap circular** — en la última gráfica, flecha derecha no sigue mostrando gráficas (clamp). (`composables/use-academy-present.js`)
- **ATLAS-7 (gráfica → mapa extendido)**: al salir por la izquierda de la primera gráfica se vuelve al **mapa normal** (cerrar visor, sin `isFullscreen`), nunca al mapa ampliado/fullscreen. El botón «🗺 Mapa ampliado» del selector sigue yendo a fullscreen a propósito.
- **ATLAS-6 (geográficas dispersas)**: las gráficas geográficas (`Dónde y cuánto`, `Latitud N→S`, `Estacionalidad por región`) se agrupan en un grupo **«Geográficas»** del selector. El resto se reparte en «Temporales» y «Diversidad y comparativas».
- **ATLAS-8 (paridad listbox ↔ selector)**: el selector/listbox de vista (misma fuente `mainViewOptionGroups`) ahora ofrece **todas las gráficas navegables del visor**, añadiendo `🏆 Ranking del grupo` y `📍 Estacionalidad por región` que faltaban. (`views/AcademyView.js`)

## 2026-06-24 — FaunaDex: conteos correctos, foto-hover y puntos de zona (PRE)
- **FD-ITEM3 (foto medalla en esquina)**: al pasar sobre una medalla del mapa, la foto representativa del grupo (de entre las del usuario) vuelve a salir en la **esquina inferior-derecha** (no pegada al cursor). El commit `75b742ab` lo había cambiado a seguir el cursor; restaurado pasando `pixelPt=null` en los `mouseover` de `useAwards.js` (POIs y cluster de zona) → `corner:true`.
- **FD-ITEM1 (nº medallas ≠ administración)**: la columna «🏅 MED.» del panel admin ahora cuenta **lo mismo que ve el usuario en el FaunaDex** = `pokedex_trophies` (Nº1) + `pokedex_awards` + `pokedex_badges` con nivel conseguido. Antes contaba solo `DISTINCT badge_key` (todos los badges, sin nivel) e **ignoraba los trofeos** → no cuadraba. Tooltip actualizado. (`backend/admin.py`, `admin/index.html`)
- **FD-ITEM2 (nº especies > Minka, imposible)**: `species/mine` y `species/zone` contaban con `species_counts` **sin filtro de rango**, incluyendo subespecies/variedades/géneros como entradas separadas → el total salía mayor que el perfil de Minka. Añadido `hrank=species` + `lrank=species` en `_build_zone_params`. Verificado en vivo para `yespi`: **818 → 736** especies (coincide con Minka). (`backend/routes_pokedex.py`)
- **FD-ITEM4 (puntos naranja de zona)**: al seleccionar una especie de «también en esta zona» cuyo id iNat **no estaba catalogado** en `academy_catalog_meta`, la resolución iNat→Minka fallaba y Minka no devolvía obs → sin puntos naranja. Añadido **fallback a `public_observations.minka_taxon_id`** (que ya guarda el mapeo para la mayoría de especies). (`backend/routes_pokedex.py`)

## 2026-06-24 — Analytics de negocio, reto semanal y robustez de mapa (PRE)
- **ANALYTICS-EVENTS**: nuevo `trackBqEvent`/`window.bqTrack` que envía eventos de negocio a **GA4** (no solo `page_view`). Instrumentado: `academy_section` (qué sección del hub engancha), `volunteer_outbound` (clicks a fuentes/convocatorias, con dominio y categoría), `weekly_challenge_click`.
- **BQ-FAUNADEX-RETOS**: «Reto de la semana» en la vista de medallas (`AwardsView`) — tarjeta motivadora determinista por nº de semana ISO, reutiliza racha/medallas ya calculadas (sin backend nuevo), con CTA a FotoFauna/mapa.
- **BQ-FAUNADEX-MOBILE**: `useMap` añade un `ResizeObserver` sobre el contenedor del mapa → reinvalida el tamaño cuando pasa de oculto a visible (tab/sheet en móvil), evitando el «mapa vacío» que los timers fijos no cubrían. Observer liberado en `destroyMap`.
- **UX-ICONS-INSHOT (lightbox Academy)**: la barra de controles del `EduLightbox` flota semitransparente con `backdrop-filter` y se colapsa al tocar la lámina (sin zoom), reapareciendo al volver a tocar.
- **SEO-04 / Core Web Vitals**: `preconnect` a `googletagmanager.com`, `youtube-nocookie.com` e `i.ytimg.com` para acelerar el LCP en páginas con vídeo.

## 2026-06-23 — Academy: Voluntariado por defecto + categorías (PRE)
- **Voluntariado** pasa a ser la **1ª pestaña** de Academy edu y la **sección que carga por defecto** (antes era Cursos). Cursos, despublicados de PRO, van al final.
- **Categorías temáticas** en Voluntariado: chips de filtro (Marino · Aves · Bosques · Murciélagos · Reptiles · Anfibios) que solo se muestran si hay contenido; al elegir una se abre el panel de fuentes.
- **7 fuentes nuevas oficiales** (Cataluña→España): SECEMU (murciélagos), AHE + SIARE/SARE (reptiles), SOS Anfibios (anfibios), Reforesta + Parcs Naturals Catalunya + CREAF (bosques), SEO/BirdLife Catalunya (aves). Cada fuente con `category`.
- Ficheros: `views/academy-edu/constants.js`, `views/AcademyEduView.js`, `views/academy-edu/EduVolunteerHub.js`, `data/edu-volunteer.json` (v3), `css/bq-edu-hub.css`.

## 2026-06-22 12:35 — BioQuest
**Build PRE:** `bqd3834ae5` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-22 09:50 — BioQuest
**Build PRE:** `bq4d5d0e23` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-22 09:44 — BioQuest
**Build PRE:** `bqa202606221144` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-21 15:28 — BioQuest
**Build PRE:** `bqdaf45162` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-21 14:26 — BioQuest
**Build PRE:** `bqa202606211626` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-21 14:20 — BioQuest
**Build PRE:** `bqa202606211620` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-21 13:54 — BioQuest
**Build PRE:** `bqa202606211552` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-20 17:29 — BioQuest
**Build PRE:** `bqa202606201929` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-20 13:52 — BioQuest
**Build PRE:** `bqa202606201552` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-19 — CC Marina: 79 láminas Wikimedia Commons (`deploy PRO`)

**Pipeline:** `edu-cc-gallery-fetch.mjs` + `edu-cc-gallery-apply.mjs` — 79/79 diapositivas con ilustración PNG desde Commons (FMIB y búsqueda temática). Créditos en caption del curso.

**B-Academy-04:** timeout 18s en búsqueda de artículos para evitar «Cargando…» infinito.

**Deploy:** PRE→PRO, smoke OK.

## 2026-06-19 13:05 — CC Marina: archivo láminas IA + fixes UX (`deploy PRO`)

**CC Marina:** 168 PNG generados por IA archivados en `/mnt/docs/bioquest/archive/cc-marina-ai-slides-20260619/` y retirados de assets activos (`edu-archive-ai-slides.mjs`). Candidatos Wikimedia Commons para d1–d2 en `cc-marina-gallery-candidates.json`.

**Fixes:** footer legal oculto en modo curso móvil (B-Academy-05); favicons orgs/eventos vía Google s2 (B-Academy-03).

**Deploy:** PRE→PRO, smoke OK.

## 2026-06-19 07:55 — Mapa invasoras: listbox restringido (`bq2e57fbf0`)

**Problema:** en el mapa «Especies invasoras en Europa» el selector permitía elegir cualquier especie del catálogo.

**Fix:** el listbox usa solo el pool invasor (`group === 'invasora'`), oculta filtros de grupo/familia, bloquea búsquedas externas y fuerza especie invasora al entrar en el modo.

**Deploy:** PRE→PRO en HanSolo, smoke OK.

## 2026-06-19 00:24 — Deploy PRE→PRO (`bq95a876f7`)

**Promoción:** rsync `public-pre/` → `public/` con backup, bump buildId, parche rutas `/pre/`→`/` en PRO, SEO sitemap/robots, smoke OK.

Incluye fix móvil (redirect `/?mobile=1`, listbox especies en overlay mapa).

## 2026-06-19 00:20 — Fix móvil PRO + listbox especies (`bqa202606190020`)

**Problemas:** BioQuest/FotoFauna no arrancaban bien en móvil (PRO); listbox de especies volvía al panel lateral izquierdo en Academy móvil.

**Causas:**
- `public/index-mobile.html` (PRO) redirigía a `/pre/` en lugar de `/?mobile=1`.
- `AcademyView.js`: `speciesListboxInSidebar` activo con sidebar abierto en móvil (`bq-mobile`).
- DNS LAN (`dnsmasq` + AdGuard) apuntaba a **localhost** (HanSolo antiguo con builds obsoletas) por auto-detect PRE más reciente.

**Fix:**
- Redirect móvil PRO → `/?mobile=1`.
- En móvil, listbox de especies solo en overlay del mapa (no sidebar).
- DNS fijado a **192.168.31.135** (`dnsmasq.conf`, AdGuard rewrites, `state/hansolo-lan-ip`).

## 2026-06-16 21:06 — CC Marina: zoom universal + láminas únicas (`bqa202606162106`)

**Problemas:** muchas diapositivas sin ampliar (SVG tenía prioridad sobre zoom; JSON sin `src` único); láminas compartidas (`bentos-pelagic-scene.png`, presets por `id`).

**Fix código:**
- `EduSlideArticle`: raster siempre antes que SVG; `rasterSrc` desde `visual.src` directo.
- `AcademyEduView`: resuelve ilustración aunque falte preset `id`.

**Fix datos:**
- `edu-sync-cc-marina-visuals.mjs`: 79 diapositivas → `./assets/edu/cc-marina/d{d}-s{ss}.png` (una lámina por slide, sin presets).
- Regeneración: `edu-generate-batch.mjs --force-regenerate cc-marina` (OpenRouter/A1111).

## 2026-06-16 20:48 — CC Marina: ampliar láminas, enlaces y galería día 10 (`bqa202606162048`)

**Problemas:** láminas con PNG (días 3–4) sin zoom porque `isSvgFigure` tenía prioridad sobre `EduSlideZoom`; enlaces `[texto](url)` se mostraban como texto plano; día 10 (galería taxonómica I) sin fotos reales bajo el texto.

**Fix:**
- `AcademyEduView`: `isSvgFigure` solo si no hay `illustration.src`; merge de `visual` conserva `src` de API.
- `formatSlideRichText` / `EduSlideArticle`: enlaces markdown → `<a target="_blank">`.
- `cc-marina.json` días 3–4: `src` en láminas generadas (`cc-marina/d*-s*.png`).
- Día 10 diapositivas 2–7: `photo_collage` con catálogo `edu-thumb-collages.json` (layout `stack-bottom`).

**Deploy PRE:** HanSolo (`bqa202606162048`).

## 2026-06-16 13:15 — CC Marina: zoom en pantalla completa + láminas ≥720p (`bqa202606160757`)

**Problema:** el botón ⛶ abría `EduImageGalleryLightbox` sin controles de zoom; muchas láminas `cc-marina` estaban a 1024×768 y se veían borrosas al ampliar.

**Fix:**
- `EduImageGalleryLightbox`: botones **− / + / ⊙**, rueda, doble clic y arrastre (pan) hasta 800 %.
- CSS galería: panel más alto, escenario zoom dedicado, imagen hasta 1280 px de ancho.
- Script `edu-upscale-min-720p.sh`: 118 láminas `cc-marina` escaladas a **1280×960** (mínimo 720p).
- Láminas compartidas (`invertebrates-plate`, etc.) ya estaban a 1536×1024.

**Deploy PRE:** HanSolo (`bqa202606160757`).

## 2026-06-16 13:10 — CC Marina: fotos a ancho completo en 1366×768 (`bqa202606160738`)

**Problema:** bandas negras laterales en fotos compactas — `max-height: 50vh` (~384px) forzaba letterboxing con `object-fit: contain`.

**Fix:**
- Altura máxima ligada al ancho columna: `min(76vh, calc(720px * 0.85))`.
- Media query escritorio bajo (≤820px alto): `70vh` / `0.9×` columna.
- Collage compact: `width: 100%`, sin `max-height` en img (altura natural).
- Anula límite 480px del layout general en slides-centered.

**Deploy PRE:** HanSolo.

## 2026-06-16 12:40 — CC Marina: fotos más altas + láminas acotadas al viewport (`bqa202606160728`)

**Problema:** fotos compactas deformadas (aspect-ratio forzado) e ilustraciones/widgets que se salían de pantalla.

**Fix:**
- `--bq-edu-slide-fig-max-h: min(50vh, 520px)` — más altura con proporción natural.
- Collage compact (1 foto): `object-fit: contain`, sin aspect-ratio, usa fig-max-h.
- Ilustraciones PNG (`.bq-edu-illustration__img`): `width: auto`, max-height acotado.
- Widgets (explore-grid, pipeline, técnicas): scroll interno si exceden viewport.
- Zoom: eliminado min-height forzado en slides-centered.

**Deploy PRE:** HanSolo.

## 2026-06-16 12:35 — CC Marina: proporción correcta + láminas dentro del viewport (`bqa202606160725`)

**Problema:** fotos deformadas (object-fit `initial` + aspect-ratio forzado) e ilustraciones que se salían de pantalla.

**Fix:**
- `--bq-edu-slide-fig-max-h: min(46vh, 480px)` — láminas zoom/SVG acotadas al viewport.
- Fotos compact (1 imagen): `object-fit: contain`, sin aspect-ratio forzado.
- Collages/triptych: `object-fit: cover` con altura máxima.
- Zoom stack-top: `width: auto`, `object-fit: contain` (proporción natural).

**Deploy PRE:** HanSolo.

## 2026-06-16 12:20 — CC Marina: láminas alineadas con columna editorial (`bqa202606160719`)

**Problema:** tras el fix a ancho completo, las imágenes ocupaban todo el viewport — más anchas que el texto y la línea del título.

**Fix:** columna editorial única `--bq-edu-slide-col: min(720px, 92vw)` para cabecera, figuras stack-top, collages y zoom. Mismo margen izquierdo/derecho que el texto; zoom sin bandas laterales se mantiene dentro de esa columna.

**Deploy PRE:** HanSolo.

## 2026-06-16 12:10 — CC Marina PRE desplegado en HanSolo + cache bust (`bqa20260616071046`)

**Problema:** cambios CSS CC Marina (láminas a ancho completo) solo en Chewie; `bioquest.yespi.es/pre/` servía build `bqa202606153060`.

**Fix:**
- CSS: `max-width: none` en canvas stack-top + visuales internos a `width: 100%` (anula columna 720px).
- Rsync `public-pre/` Chewie → HanSolo.
- Script `bump-pre-version.sh` para buildId + `?v=` + `APP_VERSION`.

**Deploy PRE:** HanSolo.

## 2026-06-16 11:00 — CC Marina: láminas sin bandas laterales (`bqa202606161100`)

**Bug:** láminas stack-top mostraban bandas gris/azul a los lados — el visor zoom forzaba `min-height` y la imagen usaba `max-height: 100%` + `object-fit: contain`.

**Fix:** zoom a altura automática; imagen `width: 100%` sin letterboxing.

**Deploy PRE:** Chewie · restart `bioquest-webapp`.

## 2026-06-16 09:00 — CC Marina: láminas a ancho completo (`bqa202606160900`)

**Bug:** láminas cinema que ocupaban todo el ancho de diapositiva quedaron recortadas tras limitar imágenes a 720px/`max-height`.

**Fix:** texto sigue centrado a 720px; figuras cinema/zoom vuelven a ancho completo; solo flotantes/asides limitados para evitar desbordamiento horizontal.

**Deploy PRE:** Chewie · restart `bioquest-webapp`.

## 2026-06-16 01:05 — Visor gráficas: zoom foto anclado abajo-derecha (`bqa202606153110`)

**Bug:** la ampliación al pasar el ratón por fotos del collage quedaba encima del panel y no se veía en miniaturas inferiores.

**Fix:** preview flotante fijo en esquina inferior derecha, junto al carrusel lateral sin taparlo.

**Deploy PRO + PRE:** HanSolo + restart `bioquest-webapp`.

## 2026-06-16 00:50 — Visor gráficas: flechas + fotos (`bqa202606153100`)

**UI:** flecha «siguiente» entre gráfica y panel lateral (ya no tapa las fotos); zoom al pasar el ratón; clic abre carrusel; ESC cierra carrusel en visor fullscreen.

**Deploy PRO + PRE:** HanSolo + restart `bioquest-webapp`.

## 2026-06-16 00:35 — Ficha: dimorfismo, crías y vídeos iNat (`bqa202606153090`)

**Bug:** desaparecieron fotos de macho/hembra/juveniles (y a veces vídeos) en la ficha de especie.

**Causa:** `/academy/species-photos` devolvía solo el carrusel en disco y no consultaba iNat para anotaciones (sexo/fase vital); el frontend solo mostraba galerías si el texto IA incluía `dimorfismo`/`crias`.

**Fix:** backend mezcla disco + iNat live para categorías vacías + vídeos/audios iNat; corrige `_medium_url` roto en anotaciones; ficha carga fotos en paralelo con species-rich; galerías visibles aunque falte texto IA.

**Deploy PRO + PRE:** HanSolo + restart `fauna_api` + `bioquest-webapp`.

## 2026-06-16 00:10 — Academy: barra lateral estable (`bqa202606153080`)

**Bug:** colapso accidental sin pulsar el botón; a veces no arrastraba; a veces OK.

**Causas:**
1. Clic en «abrir» disparaba `toggleSidebar` **dos veces** (botón + contenedor) → abría y cerraba al instante.
2. Doble clic en la barra abierta colapsaba sin querer.
3. Listeners mouse/touch sueltos podían quedar colgados tras soltar fuera de ventana.

**Fix:** un solo handler en el botón abrir; colapsar solo con botón del panel; drag con Pointer Events + `setPointerCapture`; zona de agarre más ancha; cleanup en blur/unmount.

**Deploy PRO + PRE:** HanSolo + restart `bioquest-webapp`.

## 2026-06-15 23:55 — Academy: barra lateral sin temblor (`bqa202606153070`)

**Bug:** la barra central de redimensionar/colapsar vibraba o colapsaba de golpe; al pulsar «colapsar» el layout parecía redimensionarse en bucle.

**Causa:** transiciones CSS simultáneas (ancho sidebar + flex-basis barra + altura mapa) y `invalidateSize()` de Leaflet en cada frame del drag.

**Fix:** clase `ac2-layout-stable` durante colapso y drag (transitions off); `invalidateSize` solo al soltar; `@mousedown.stop` / `@dblclick.stop` en la barra; reglas hover duplicadas eliminadas.

**Deploy PRO + PRE:** HanSolo + restart `bioquest-webapp`.

## 2026-06-15 23:45 — OAuth con hash profundo (`bqa202606153060`)

**Bug:** Google login fallaba en vistas con hash `#atlas?taxon=…` — `auth_token` acababa en el fragmento; el 2.º clic funcionaba vía cookie refresh.

**Fix:** backend `_append_auth_token_to_url`; frontend lee token del hash; guarda/restaura URL pre-OAuth; log en `sessionStorage.bq_auth_log`.

**Deploy PRO + PRE:** HanSolo + restart `fauna_api`.

## 2026-06-15 23:30 — Google login UI + FF sin popup (`bqa202606153050` / `ec202606153050`)

**BQ:** tras OAuth la nav seguía en «Acceder» hasta el 2.º clic — `loadMe` en background + `ff_logout_skip` bloqueaban hidratación. Ahora: flag `bq_oauth_return`, `await loadMe` antes de mostrar nav, `applySession` + hydrate al boot.

**FF:** mismo flujo Google que BQ (refresh → redirect, sin popup).

**Deploy PRO + PRE:** HanSolo.

## 2026-06-15 23:20 — Google login sin popup (`bqa202606153040`)

**Bug:** «Continuar con Google» abría un popup blanco ~2 s aunque el usuario ya estuviera en Google / FF.

**Fix:** 1) intentar `/auth/refresh` (cookie FF compartida) → login instantáneo; 2) si hace falta Google, redirect en la misma pestaña (sin `popup=1`); backend reutiliza sesión `ff_refresh` en `/google/login`.

**Deploy PRO + PRE:** HanSolo + restart `fauna_api`.

## 2026-06-15 23:10 — Minka compartido FF↔BQ (`bqa202606153030`)

**Bug:** tras login, BQ pedía configurar Minka aunque ya estuviera en FotoFauna (misma cuenta).

**Causa:** onboarding comprobaba solo `minka_username`; en FF suele guardarse en `minka_login`. El endpoint no devolvía `login`.

**Fix:** detección vía `has_minka_pw` (como FF), fallback `/auth/me` → prefs, backend devuelve `login` + `username` con fallback cruzado.

**Deploy PRO + PRE:** HanSolo + restart `fauna_api`.

## 2026-06-15 23:00 — Logout persistente (`bqa202606153020`)

**Bug:** tras «Cerrar sesión» la sesión se reabría sola (refresh en vuelo + cookie `yespi_access` / token residual).

**Fix frontend (`useAuth.js`):** `ff_logout_skip` se activa antes de limpiar; `loadMe`/`_setToken`/postMessage respetan el flag aunque quede Bearer; se cancela refresh paralelo.

**Fix backend (`auth.py`):** `POST /auth/logout` ya no exige Bearer válido — borra cookie `ff_refresh` siempre.

**Deploy PRO + PRE:** HanSolo + restart `fauna_api`.

## 2026-06-15 22:40 — Modal Acceder compacto (`bqa202606153010` / `ec202606153010`)

**UI:** `.bq-modal--auth` — padding, campos y botones más pequeños; tabs con clase CSS; «Crear cuenta» (4 campos) cabe sin scroll en viewport habitual.

**Deploy PRO + PRE:** HanSolo.

## 2026-06-15 22:30 — Acceder unificado + fix logout (`bqa202606153000`)

**Nav:** botón invitado renombrado a **«Acceder ▾»** (antes «Cuenta»).

**Logout:** flag `ff_logout_skip` evita re-autologin por cookie refresh tras cerrar sesión (ya no se reabre sola).

**Deploy PRO + PRE:** `bqa202606153000` — HanSolo.

## 2026-06-15 21:50 — Nav, modales y ayuda (`bqa202606152810`)

**Modal «Cómo funciona»:** acordeón exclusivo (abrir uno cierra los demás); modal teletransportado al `<body>` con `z-index: 100000` por encima de la barra; botón ✕ siempre accesible en toolbar sticky.

**Login / onboarding:** mismos modales por encima del nav (ya no quedan tapados).

**Nav:** logo pegado al borde izquierdo; pestañas Atlas / Academy / FaunaDex junto al logo (sin hueco de `--bq-brand-w`).

**Eliminado:** banner duplicado «Cómo funciona BioQuest» bajo la barra superior.

**Caché:** `buildReloadOnce()` activo también en PRO (no solo `/pre/`).

**Deploy PRO + PRE:** `bqa202606152810` — HanSolo.

## 2026-06-15 21:40 — FotoFauna: restaurar menú inicio (`ec202606152720`)

**Revert:** botones `fsh-btn` / `fsh-btn--accent` para Iniciar sesión y Registrarse (sustituían `ff-cross-link` sin estilo — pills blancos). Usuario logueado vuelve a `fsh-user-btn` con nombre + ▾. Ayuda `?` visible también con sesión. Se mantiene menú compartir.

## 2026-06-15 21:32 — Compartir enlace → toast «Enlace copiado» (`bqa202606152710`)

**Fix:** el icono «Compartir enlace» del menú compartir copia la URL al portapapeles y muestra toast «Enlace copiado» (antes abría Web Share API sin feedback).

**Deploy PRO:** `bqa202606152710` — HanSolo.

## 2026-06-15 21:28 — Logo nav + «Cómo funciona BioQuest» visible (`bqa202606152700`)

**Logo:** nav más alta (64px), logo ~50px de alto (antes ~46px en barra de 56px). En móvil el logo ya no se oculta — versión compacta 34px.

**Ayuda:** botón con texto «Cómo funciona BioQuest» en la barra (❓ en móvil). Banner bajo nav en todas las pestañas (no solo Atlas); clave localStorage `bq_help_banner_v2`.

**Deploy PRO:** `bqa202606152700` — HanSolo.

## 2026-06-15 21:23 — Logout permanece en BioQuest (`bqa202606152630`)

**Fix:** `logout()` ya no redirige a `/ff-api/pre/?logout=1` (FotoFauna). Llama `POST /ff-api/auth/logout`, limpia tokens locales y recarga BioQuest en el hash actual (`#atlas`, etc.).

**Deploy PRO:** `bqa202606152630` — HanSolo.

## 2026-06-15 17:32 — BioQuest
**Build PRE:** `bqa202606152030` · **Origen:** sync automático (Chewie)

- Cambios en working tree pendientes de commit (sync automático).


## 2026-06-13 — Compartir enlaces + deep links (BQ-SHARE)

**Compartir (Fase 1):** botón 🔗 en la barra superior con menú: Web Share API, copiar enlace,
WhatsApp, Telegram, X/Twitter e hint Instagram (copia enlace). Toast de confirmación.

**Deep links (Fase 2):** estado en hash sin recargar:
- Atlas: `#atlas?taxon={inat_id}&view={chart:…}`
- Academy curso: `#academy?course=cc-marina&day=2&slide=3`
- Academy hub: `#academy?edu=media&q=Attenborough`

**Routing:** `useBqShareUrl.js` parse/build/sync · integrado en `useBqHashRouting.js`,
`AcademyView.js` (taxon + vista gráfica), `AcademyEduView.js` (curso/slide/hub).
Legacy `?taxon=` en query sigue abriendo Atlas.

**Pendiente:** BQ-SHARE-06 OG dinámico por vista.

**Deploy PRO:** `bqd0f6bcac` — smoke OK.

## 2026-06-08 — Caché de fotos en disco + precache masivo + favicons (`bq044net1`)

**Intermitencia carrusel Atlas / thumbnails FaunaDex.** Causa: el proxy
`/ff-api/academy/photo-proxy/{id}/{file}` iba a iNaturalist **en vivo en cada petición**
(~1.2s/foto, sin caché en disco). Con 8-12 fotos por carrusel, algunas no llegaban a tiempo
→ "a veces carga, a veces no".

**Solución (backend `routes_academy.py`):** `photo_proxy_inat` ahora **cachea en disco**
(`/app/academy-thumbs/_proxy/{id}/{file}`, volumen persistente). 1ª petición descarga y
guarda; siguientes se sirven de disco (medido: 1.33s → **0.011s**, 120× más rápido). La
caché crece sola con el uso real. `_download_bytes` reforzado con **backoff exponencial +
Retry-After** ante 429/503 para no sobrecargar iNat/Minka.

**Precache masivo** (`backend/bq_precache_all_thumbs.py`): carrusel + categorías de TODAS
las especies relevantes = catálogo Atlas (~170) ∪ `DISTINCT taxon_id` de `user_observations`
(~1840) = ~2000 únicas (~1.1 GB). `only_missing` → idempotente. Detecta automáticamente
especies de **usuarios nuevos** en cada ejecución (lee la BD en vivo).
- Batch inicial: background, 10s/especie (margen a iNat).
- Cron diario 04:00: `/mnt/scripts/fauna/bq-precache-all-thumbs-daily.sh` (ver `FAUNA_CRONS.md`).

**Favicons 404** (`EduOrgsHub.js`, `EduVolunteerHub.js`): añadido `@error` a los `<img>` de
favicon → un favicon externo inexistente (Google `s2/favicons`) se oculta en vez de dejar
icono roto. (El 404 de red del recurso externo no es suprimible desde JS; solo el efecto visual.)

## 2026-06-08 — Fix definitivo errores de red Academy (`bq044net1`)

**Síntoma:** cascadas de `ERR_ADDRESS_UNREACHABLE` / `ERR_CONNECTION_REFUSED` /
`ERR_NETWORK_CHANGED` contra `/ff-api/academy/*` y "No se pudo cargar el catálogo" en
`bioquest.yespi.es/pre/#academy`.

**Causa raíz (no era la máquina, ni AdGuard, ni Cloudflare):**
- `bioquest-webapp` **no tenía IP estática** en `yespi-net` → cogía dinámicamente la `.0.4`,
  que es la **reservada de vaultwarden** → colisión latente y cambios de IP al recrear.
- El `location /ff-api/` del nginx interno hacía `proxy_pass http://fauna_api:3005/` **sin
  `resolver`** → nginx cacheaba la IP al cargar; cuando `fauna_api` se recreaba (otra IP),
  seguía enviando a la IP muerta.

**Solución (definitiva y transversal):**
- IP estática `172.19.0.12` para `bioquest-webapp` (compose). Todo el stack `yespi-net` queda
  con IP fija — ver [`/mnt/docs/infra/Red-yespi-net.md`](../../infra/Red-yespi-net.md).
- `resolver 127.0.0.11` + upstream en variable + `proxy_next_upstream` en `/ff-api/`
  (`webapp/nginx.conf`), igual que el resto de `location` del fichero.
- Catchall NPM transversal: eliminada la lista manual de exclusiones por app (nginx prioriza
  `server_name` exacto sobre el regex).

**Parches retirados** (`composables/bq-wait-api.js`): `waitForFfApi` en bucle de espera y la
clasificación específica de errores de red (`isHardNetworkFail`, `isServerUnreachableErr` y
los tokens `network_changed`/`unreachable`/`err_address`). Queda solo un wrapper de fetch
genérico con timeout + 1 reintento suave. `useAuth.js`/`useSpecies.js`/`bq-import-retry.js`
**conservan** su detección de red genérica (cubre cortes de WiFi reales del cliente).

## 2026-06-06 — 7×15 diapositivas + generación nocturna PRE (`bq048doc1`)

- Curso **1 semana** (7 días × ~15 diapositivas). Visor tema claro, lámina fullscreen, zoom.
- **cc-terrestre** esqueleto completo en castellano.
- Scripts nocturnos SD local: [`ACADEMY_ILLUSTRATIONS_BATCH.md`](ACADEMY_ILLUSTRATIONS_BATCH.md).

## 2026-06-05 — Ilustraciones lámina curso PRE (`bq047illu1`)

- Tipo visual `illustration` / PNG en `assets/edu/` (6 láminas piloto).
- 13 lecciones cc-marina pasan de SVG-cajas a ilustraciones cohesivas.
- Doc actualizada: [`ACADEMY_SLIDES.md`](ACADEMY_SLIDES.md) — estrategia lámina vs fallback SVG.

## 2026-06-05 — Visor curso inmersivo + Biología marina básica PRE (`bq046edux1`)

- **UI:** índice lateral por módulos, diapositiva a pantalla completa, navegación ←/→ y ESC.
- **Contenido:** curso `cc-marina` renombrado a *Biología marina básica* — 21 lecciones en 8 módulos según temario Gustavo.
- **Visuales SVG nuevos:** factores marinos, pisos litorales, taxonomía, peces, plancton, Posidonia, relieve, técnicas CC.
- Doc: [`CURRICULUM_MARINA_BASICA.md`](CURRICULUM_MARINA_BASICA.md) con preguntas de evaluación.

## 2026-06-08 — Formato diapositiva cursos PRE (`bq045slides1`)

- Criterio editorial: concepto + gráfico + ejemplos ([`ACADEMY_SLIDES.md`](ACADEMY_SLIDES.md)).
- Visuales SVG: ciencia ciudadana, zonas océano, bentos vs pelágico.
- cc-marina días 1–7 reescritos como diapositivas piloto.

## 2026-06-07 — ESC vuelta curso Academy desde Atlas PRE (`bq044edu3`)

- Contexto de retorno en sessionStorage al abrir mapa desde lección.
- Botón flotante en Atlas y **ESC** restauran `#academy` en el día del curso.

## 2026-06-07 — Fix clics Academy cursos PRE (`bq044edu2`)

- **Bug:** `AcademyEduView` importaba Vue desde `/vendor/` (segunda instancia) → reactividad rota, clics sin efecto.
- Fix imports `../vendor/` + `useAcademyEduProgress` paths; build nav sincronizado con `version.json`.

## 2026-06-07 — Progreso edu BD + certificado PRE (`bq044edu1`)

- Tabla `academy_edu_progress` (auto-create al primer uso).
- API: `GET/POST /academy/edu/progress/*`, certificado al completar cc-marina.
- `AcademyEduView` + `useAcademyEduProgress`: marcar día, reanudar, localStorage invitados.

## 2026-06-07 — cc-marina 21 días + P1.5 composables PRE (`bq043p151`)

- **Curso cc-marina:** contenido completo días 1–21 (hábitats, proyectos, cierre).
- **P1.5:** `useAcademyCharts` (ayuda + insight) y `useAcademyMapRender` (scheduler rAF) extraídos de `AcademyView.js`.
- Recompute academy cache relanzado vía `bq-recompute-academy-cache.sh`.

## 2026-06-07 — P3 fase 2 cc-marina días 4–7 PRE (`bqp3edu2`)

- Curso piloto ampliado a **7 días** (cetáceos, aves marinas, invasoras, repaso semana 1).
- `AcademyEduView`: límite navegación por `pilot_days`, etiqueta dinámica en tarjetas.
- Audits Playwright → `#atlas` (smoke, deep, marathon, mobile, webs, benchmark).

## 2026-06-07 — P3 fase 2 curso piloto PRE (`bqp3edu1`)

- **Curso cc-marina:** días 1–3 con contenido, tareas de campo y enlaces a Atlas.
- **API:** `GET /academy/edu/courses/{id}/day/{n}`.
- **AcademyEduView:** navegación curso/día, botones → `#atlas`.
- **P2:** WDPA import completo (4020 áreas); ai-warm + backfill CR×50 en background.
- Playwright audits apuntan a `#atlas`.

## 2026-06-07 — P3 Academy + swap Atlas/Academy PRE (`bqp3swap1`)

- **Naming swap:** 3 pestañas — **Atlas** (`#atlas` = mapas, ex-AcademyView), **Academy** (`#academy` = landing educativa), **FaunaDex**.
- **`AcademyEduView`:** cursos preview, documentales y organizaciones desde `data/edu-seed.json`.
- **API:** `GET /academy/edu/courses`, `GET /academy/edu/seed`.
- **P2b:** capas buques `vessels_es/ngo/foreign/other` con colores azul/naranja/morado/gris.
- **P2 background:** backfill amenazadas + import WDPA wave iniciados.
- Rollback: `migration-pack-naming/scripts/rollback-swap.sh`.

## 2026-06-07 — P1 rendimiento Academy PRE (`bq042perf2`)

- **`fetchCatalogOnce`**: `loadList` reutiliza prefetch del módulo (sin `force=true` al boot).
- **Conservation map**: debounce 750 ms + umbral 12 % bbox antes de refetch áreas/buques.
- **CSS**: ~1035 líneas Academy (v3/v4) migradas de `index.html` → `bq-academy-v2.css` (−38 % HTML).

## 2026-06-07 — P1 rendimiento Academy PRE (`bq041perf1`)

- **`renderCurrentFrame`**: coalescing con `requestAnimationFrame` en scrub, animación y watcher de frame; flush inmediato al pausar/seleccionar especie.
- **`_fetchSpeciesAnalytics`**: dedup inflight, caché 45 s por especie/filtros, retries reducidos (2×1,5 s en lugar de 4×2 s).

## 2026-06-07 — P0 optimización Academy PRE (`bq040optim1`)

- **Playwright**: selectores `.ac2-sp-row` → `.ac2-tax-result` en audits desktop/marathon/móvil.
- **Guard amenazadas**: `_isAmenMapSpecies` con fallback `miteco_marine` simétrico al guard CR.
- **Mapas especiales**: sin auto-POI al entrar en CR/amenazadas (evita race UX).
- **Conservation cleanup**: `resetConservationOnMapDestroy()` aborta fetches, timers y capas GFW.
- **`window.online`**: listener nombrado y eliminado en unmount (`AcademyView`).
- **Backend catálogo**: sin fallback `minka_live` en IDs desconocidos — coherencia post-limpieza Minka.
- **`conservation-brief`**: fallback BD (`conservation_store`) + geojson local cuando WDPA no resuelve `site_id`.
- Build PRE: `bq040optim1` · backup: `backup_bioquest_pre_20260607_203910.zip`.

## 2026-06-07 — Fix cambio de especie en gráficas (PRE `bq039spfix2`)

- Al elegir otra especie en el listbox del visor de gráficas, las analíticas (`detailStats`, `yearTrend`, series temporales) se limpian y recargan siempre.
- Mapas CR/MITECO: `selectSpecialMapSpecies` ya no deja datos de la especie anterior en las gráficas.
- En visor a pantalla completa se permite cualquier especie del catálogo (sin bloqueo por mapa especial).
- Evita carrera del watcher `sourceFilter` al resetear filtros; refresco forzado de gráficas dependientes de caché (`source`, `migration`, `multi`).
- **`bq039spfix2`**: serializa clics/teclado en el listbox (ignora respuestas obsoletas; debounce 240 ms en ↑/↓) — corrige el fallo intermitente «a veces sí, a veces no».
- **Deploy PRO** 2026-06-07 ~18:13 (`bioquest.yespi.es/` → build `bq039spfix2`).

## 2026-06-07 — Migration pack naming (swap en bloque)

- Paquete paralelo `./docker/bioquest/webapp/migration-pack-naming/` — overlay Atlas/Academy sin tocar PRE hasta `apply-swap.sh`.
- Doc: [`MIGRATION_PACK.md`](../../_archive/2026-06-bioquest/MIGRATION_PACK.md).

## 2026-06-07 — Naming: Atlas · Academy · FaunaDex+Medallas

- Decidido: **Atlas** (`#atlas`) = mapas/gráficos actuales; **Academy** = formación nueva; **Medallas** dentro de **FaunaDex** (`#explore/medals`).
- Docs: [`NAMING_EJES.md`](NAMING_EJES.md), [`PROPUESTA_ACADEMY.md`](../../_archive/2026-06-bioquest/PROPUESTA_ACADEMY.md); `PROPUESTA_BIOHUB.md` obsoleto.

## 2026-06-07 — Backlog BioHub, buques ecológicos y docs

- [`TAREAS_PENDIENTES.md`](../../TAREAS_PENDIENTES.md): buques por categoría (ES/ONG/extranjero), background continuo, tareas riesgo cero, BioHub.
- [`PROPUESTA_BIOHUB.md`](../../_archive/2026-06-bioquest/PROPUESTA_BIOHUB.md): nueva sección `#biohub` — documentales, organizaciones, 5 cursos IA interactivos.
- [`BUQUES_ECOLOGICOS.md`](BUQUES_ECOLOGICOS.md): taxonomía colores mapa y ampliación catálogo AIS.
- [`ARQUITECTURA.md`](ARQUITECTURA.md): endpoints buques/conservación, pipeline background, roadmap BioHub.

## 2026-06-07 — Auditoría y limpieza IDs Minka↔iNat

- Causa: descargas con `taxon_name` mezclaban especies (ej. Dama dama ← 896 obs de *Vanessa cardui*).
- `bq_download_minka.py`: usa `taxon_id` Minka + validación por observación.
- `resolve_minka_taxon_id`: ya no toma el primer autocomplete a ciegas.
- Scripts: `bq_audit_minka_ids.py`, `bq_clean_minka_cross.py` (eliminadas 2654 obs cruzadas).
- Meta corregido: Prolemur simus (antes apuntaba a *Eulemur fulvus*).

## 2026-06-07 — Clasificación unificada de especies + auto-alta (PRE `bqclassify1`)

- `academy/species_registry.py`: reino (marino/terrestre), flags CR/MITECO/invasora, validación.
- Catálogo corregido: duplicado carey, grupos taxonómicos (algas, cnidarios, ave_marina, molusco…).
- MITECO derivado del catálogo (`miteco_marine: true`) — sin lista duplicada.
- Alta de especies usuario: `classify_taxon()` + `validate_species_entry()` automático.
- Script `bq_validate_catalog.py` y endpoint admin `GET /academy/catalog-validate`.

## 2026-06-07 — Filtro tierra/mar en todos los mapas Academy (PRE `bqrealm1`)

- Backend: `maybe_snap_point` / `snap_poi` con validación estricta de reino (marino ↔ agua, terrestre ↔ tierra).
- Máscara auxiliar interior ibérico (falsos negativos del shapefile 110m).
- POIs amenazadas MITECO y CR mundial snapeados; POI local inválido → referencia MITECO.
- Pardela balear: grupo catálogo `ave_marina` (antes `ave` la empujaba a tierra).
- Series precache y `/timeseries` filtran puntos incoherentes al servir caché.
- Frontend: mapas especiales cargan su propia serie y limpian heatmap obsoleto al cambiar especie/modo.

## 2026-06-07 — Sync cache-bust + banner red módulos (PRE `bqamenfix1`)

- Todos los `?v=` de PRE unificados a `bqamenfix1` (index, AcademyView, composables).
- Banner «Recargar» si falla la carga de `.js` por `ERR_NETWORK_CHANGED` / `CONNECTION_REFUSED`.

## 2026-06-07 — Fix crash mapa amenazadas MITECO (PRE `bqamenfix1`)

- `amenListboxGroups`: optional chaining si categoría vacía o API caída (`ERR_NETWORK_CHANGED`).
- Lista MITECO se vacía con mensaje de error en lugar de dejar Vue roto.

## 2026-06-07 — Comparador: misma barra degradada que mapa normal (PRE `bqoverlay2`)

- Toolbar 50/50 unificada (gradiente vertical + blur); fichas A/B y «Salir» al estilo listbox/play del mapa.

## 2026-06-07 — Barra fullscreen = mismo degradado que mapa normal (PRE `bqoverlay1`)

- Eliminada la tarjeta sólida en fullscreen; misma UX de gradiente vertical + blur que el mapa embebido.

## 2026-06-07 — Dock comparar A|B + barra fullscreen (PRE `bqcompare1`)

- Botón comparador (icono A|B cian/naranja) junto a pantalla completa, normal y fullscreen.
- Barra overlay en fullscreen con gradiente, borde accent y más aire.
- Eliminado el texto «⇔ 50/50» suelto; acción unificada en dock superior derecho.

## 2026-06-07 — UX layout mapa 50/50 (PRE `bqsplitui1`)

- Barra play con margen superior y estilo flotante (más aire respecto al nav).
- Fichas A/B más arriba y compactas; mapas con padding lateral y bordes redondeados.

## 2026-06-07 — Play en mapa 50/50 (PRE `bqsplitplay1`)

- Barra del modo comparar: ▶ play, ↺ reinicio, mes, velocidad y scrubber temporal.
- Animación sincronizada en mapas A y B (misma lógica de ventana móvil que el mapa principal).

## 2026-06-07 — Fullscreen respeta nav + controles visibles (PRE `bqfsfix2`)

- Mapa fullscreen empieza debajo de `bq-nav-shell` (logo BioQuest y pestañas siempre visibles).
- Botones ⋮ / ⛶ / ⇔ 50/50 por encima del overlay (`z-index` 9200).
- Listbox de vista restaurado en layout v2 + fullscreen.

## 2026-06-07 — Fix fullscreen mapa tiling (PRE `bqfsfix1`)

- Revertido `overflow:visible` en contenedor fullscreen (rompía Leaflet → mapa repetido horizontalmente).
- Contenedor fijo `100dvh`, scroll del `body` bloqueado, `invalidateSize` con reintentos.
- En fullscreen: selector de vista nativo (`<select>`); paneles listbox con `position:fixed`.
- Mapa principal oculto en modo 50/50 para evitar capas duplicadas.

## 2026-06-07 — Mapa 50/50 UX foco + fullscreen (PRE `bqsplit2`)

- Pantalla completa automática al entrar; ESC o «Salir» restaura el estado anterior.
- Ficha flotante sobre cada mapa (nombre + «Pulsa para seleccionar»); selector anclado al panel activo.
- Tras elegir A, foco automático en B para elegir la segunda especie.

## 2026-06-07 — Mapa 50/50 comparar 2 especies (PRE `bqsplit1`)

- Modo **⇔ Mapa 50/50** (menú ⋮ del mapa): dos mapas sincronizados, heatmap acumulado A (azul) / B (naranja).
- `useAcademySplitMap.js` — solo en modos `anim` e `invasoras`.

## 2026-06-07 — Export PNG visor gráficas (PRE `bqexport1`)

- Botón **📥** en cabecera del visor: descarga la gráfica ampliada como PNG (sin dependencias; `chart-export.js`).

## 2026-06-07 — Visor: atajos + badge frescura stats (PRE `bqshortcuts1`)

- **Atajos visor:** overlay `?` con ESC, ←/→ y botones laterales; teclado en `use-academy-present.js`.
- **Badge ficha:** «Datos de hace X d» desde `cache_updated_at` en `/academy/species-stats` (precache `academy_taxon_cache`).

## 2026-06-07 — Checkpoint PRO (`bq4b969604`)

- **Visor gráficas:** cambio de especie en panel A/D sin reinicializar mapa (`pickSpeciesFromListbox` + `chartsOnly`).
- **Mapas especiales:** selección ligera CR/amenazadas (`selectSpecialMapSpecies`); fix heatmap canvas 0 px; export `crSpeciesList`.
- **Thumbnails:** API solo devuelve `thumb_url` si existe en disco; fix HEAD en `/academy/thumbs/`; deploy cache-bust en todos los módulos JS.
- **CR ×50:** catálogo ampliado; listbox CR restringido al inventario (no catálogo general).
- **Backend:** `critico_map`, `amenazadas_map`, `species.py`, `routes_academy` thumbs HEAD.
- **Listbox amenazadas solo MITECO** (mismo patrón que CR): sin catálogo general en cabecera; guard en `selectSpecies`.
- **Thumbs:** skip rápido si `carousel_0.webp` ya existe; precache dirigido de las 7 pendientes.

## 2026-06-07 — CR icónicas ×50 + animación temporal (PRE `bq318cr50`)

- **Mapa CR ampliado** de 20 → **50 especies icónicas** mundiales (catálogo + sync iNat/Minka).
- **Overlay animado** en mapa CR: heatmap mensual de **una especie a la vez**; POIs CR permanecen visibles (mismo patrón que amenazadas MITECO).
- Scripts: `bq_download_critico.py`, `bq-critico-obs-backfill.sh`.

## 2026-06-07 — Amenazadas: animación temporal + thumbs (PRE `bq318amen6`)

- **Overlay temporal** en mapa MITECO: al seleccionar una especie se carga su timeseries (heatmap + play/scrubber) **sin quitar** los POIs del inventario (29 marcadores UICN).
- **Thumbnails:** la API solo devuelve `thumb_url` si existe `carousel_0.webp` en disco (evita 404 en consola); script `bq-amenazadas-thumbs.sh` para precache de las 29 especies.

## 2026-06-07 — Backfill obs MITECO (iNat + Minka)

- Script `bq_download_amenazadas.py` + `bq-amenazadas-obs-backfill.sh`: descarga **ambas fuentes** para las 29 especies del inventario MITECO.
- POIs locales sustituyen marcadores de referencia cuando hay datos en `public_observations`.

## 2026-06-07 — Amenazadas: listbox en ficha + catálogo general (PRE `bq318amen4`)

- **Inventario MITECO** movido al panel lateral (ficha): lista agrupada con colores UICN; colapsable al tener especie seleccionada.
- **Catálogo general** en cabecera del panel (como mapas anim/expansion): cualquier especie del catálogo (general, CR, invasora).
- Panel conservación MITECO solo si la especie está en el inventario de 29.

## 2026-06-07 — Ficha amenazadas ampliada (PRE `bq318amen3`)

- **Panel de conservación destacado** en ficha del mapa MITECO: cabecera con badge UICN, población estimada, amenazas en lista, marco legal Directiva Hábitats, enlaces MITECO/BOE/UICN y TTS.
- **API** enriquecida: `threat_drivers`, `protection_note`, `iucn_ai_extra`; fichas completas para las 29 especies del inventario.

## 2026-06-07 — Mapa amenazadas: listbox + colores UICN (PRE `bq318amen2`)

- **Listbox flotante** en modo «Especies amenazadas»: 29 especies agrupadas por categoría MITECO (aves, moluscos, mamíferos, reptiles); selección centra el mapa y abre ficha.
- **Marcadores por nivel UICN** (CR/EN/VU/NT/LC) con leyenda en el mapa; colores estándar UICN en preview y listbox.
- **Ficha enriquecida:** categoría UICN, anexo Directiva Hábitats, nota de población (cuando existe) y resumen de amenazas por especie.
- **API** `GET /academy/threatened-marine`: campos `iucn_category`, `iucn_color`, `threat_summary`, `population_note`, `habitat_annex`, `iucn_legend`.

## 2026-06-07 — Mapa especies amenazadas MITECO (PRE `bq318amen1`)

- **Nuevo modo mapa** «🛡️ Especies amenazadas (Directiva Hábitats)»: 29 especies del [Inventario Español de Especies Marinas (MITECO)](https://www.miteco.gob.es/es/biodiversidad/temas/biodiversidad-marina/habitats-especies-marinos/inventario-espanol-habitats-especies-marinos/list-inventario-especies-marinas.html).
- **API** `GET /academy/threatened-marine` — POIs por observaciones locales + nota legal [Directiva 92/43/CEE (BOE)](https://www.boe.es/doue/1992/206/L00007-00050.pdf).
- **Catálogo:** +18 cetáceos/foca/pardela/lapa del inventario para sync nocturno.

## 2026-06-07 — Deploy PRO P0/P1 (`bq454dfdc9` BioQuest · `db46ff9c` FotoFauna)

- **BioQuest PRO:** gráficas hora+YoY, filtro familias (`academy_catalog_meta`), enlace `#academy?taxon=`, dual iNat/Minka.
- **FotoFauna PRO:** banner error persistente admin Usuarios.
- **Backend:** `obs_hour`, `catalog_meta`, sync Minka bbox Europa, módulos `ranking`/`iucn`/`invasive`/`tts`.

## 2026-06-07 — P2/P3 rendimiento (PRE, pendiente deploy)

- **P2 frontend:** boot Academy sin IUCN/GFW/effort-stats; carga lazy al abrir Panel A/C o mapa conservación; `AbortController` en fetch WDPA/GFW/buques.
- **P3 operación:** cron Academy 02:00 (no compite con fauna 01:00); ventana sync 3 h; script `bq-playwright-smoke-hourly.sh`.

## 2026-06-07 — Dual iNat/Minka: bbox España + mapeo taxon_id

- **Descarga Minka** (`bq_download_minka.py`): filtro geográfico bbox Europa (como iNat) para priorizar obs en España/península.
- **Meta catálogo** (`academy_catalog_meta`): columna `minka_taxon_id` + resolución por nombre científico vía API Minka.
- **Docs:** `ARQUITECTURA.md` — sección clave dual iNat/Minka.

## 2026-06-07 — Fix familias núcleo + acceso YoY (PRE `bq318p0p2`)

- **Familias:** tabla `academy_catalog_meta` + backfill iNat para ~80 especies núcleo (p. ej. Ostreidae). Filtro listbox calculado desde catálogo enriquecido.
- **YoY accesible:** entradas directas en listbox «Vista» (`📊 YoY`, `🕐 Hora`); badge «años · YoY» en ficha abre la gráfica al pulsar.

## 2026-06-07 — P0/P1 Academy (PRE `bq318p0p1`, sin deploy PRO)

- **Gráficas hora del día + YoY:** modos `hour` y `yoy` en visor ampliado; mini-tarjetas en Panel A y Panel D.
- **Backend:** columna `public_observations.obs_hour`; `species-stats` devuelve `by_hour` / `hour_obs`; descargas iNat guardan hora desde `time_observed_at`.
- **Familias taxonómicas:** tira de filtro por familia en listbox de especies; API `families` en `/academy/target-species`.
- **Enlace compartible:** `#academy?taxon=<inat_id>` abre la especie al cargar (limpia query tras seleccionar).
- **Admin P0:** banner persistente de error en pestaña Usuarios (además de toast) con indicación de reintentar en LAN.

## 2026-06-07 — Deploy PRO (`bqba5cd273` BioQuest · `c9828da5` FotoFauna)

- **BioQuest PRO:** OAuth Google popup, proxy fotos ficha/carrusel, borrado suave catálogo 7 d.
- **FotoFauna PRO:** admin Academy gestión masiva catálogo, sync sesión BQ iframe, fixes auth.
- **Backend:** `catalog_deleted`, `catalog_extra`, admin academy bulk, `photo-proxy` reforzado.
- **Backup:** `HanSolo_Integral_20260607_114716.tar.gz` (94M, subido a Drive).

## 2026-06-07 — Academy: fix thumbnails ficha/carrusel (PRE `bq317tax16`)

- **Causa:** fotos iNat (S3/static) llegaban al navegador sin proxy; en LAN → `ERR_ADDRESS_UNREACHABLE` en `medium.jpg`.
- **Frontend:** todas las URLs de ficha/carrusel pasan por `/ff-api/academy/photo-proxy/`; se rechazan rutas rotas (`medium.jpg` suelto).
- **Backend:** `_medium_url` usa `medium_url`/`large_url`; `species-photos` solo devuelve URLs proxyables.
- Galería de ficha oculta celdas si la imagen falla (`@error`).

## 2026-06-07 — Fix login Google embebido (PRE `bq317tax15`)

- **Causa:** el enlace «Continuar con Google» navegaba dentro del iframe; Google OAuth no permite iframes → «Este contenido está bloqueado».
- **BioQuest:** OAuth en ventana popup (`popup=1`); si el popup falla, redirección en ventana superior (sale del iframe).
- **Backend:** `postMessage` del callback OAuth usa `'*'` para que el token llegue a BioQuest (origen distinto de FotoFauna).
- **FotoFauna:** sincroniza sesión cuando BioQuest notifica login vía `bq_to_ff`.

## 2026-06-07 — Admin Academy: borrado / recuperación masiva catálogo

- **Panel Admin → ACADEMY:** sección «Gestión del catálogo» con búsqueda, filtro (activas / pendientes), selección múltiple y acciones masivas.
- **Backend:** `GET /admin/academy/catalog`, `POST /admin/academy/catalog/soft-delete` y `POST /admin/academy/catalog/restore` con `{ inat_ids: [] }`.
- Texto de ayuda actualizado: borrado suave 7 días (no borrado inmediato).

## 2026-06-07 — Academy: re-alta cancela borrado pendiente

- Si una especie está en periodo de gracia y alguien la vuelve a añadir (`catalog-request`), se cancela el borrado pendiente y vuelve al catálogo visible (especies núcleo e incluidas por usuario).

## 2026-06-07 — Auth iframe FotoFauna + session-start (PRE `bq317tax14`)

- Reintentos en `/usage/session-start` y `/auth/me` tras login Google (mitiga `ERR_NETWORK_CHANGED` al abrir BioQuest embebido).
- FotoFauna reenvía token al iframe cuando la sesión queda lista; precarga del iframe después del OAuth.

## 2026-06-07 — Academy: borrado suave catálogo 7 días (PRE `bq317tax13`)

- **Eliminar cualquier especie (admin):** núcleo y añadidas por usuario; desaparece al instante del catálogo visible.
- **Periodo de gracia 7 días:** observaciones, ficha IA, caché e imágenes se purgan definitivamente tras una semana (cron nocturno + arranque API).
- **Recuperación admin:** sección «Pendientes de borrado» con cuenta atrás (p. ej. `6d 12h`) y botón ♻️ restaurar.

## 2026-06-07 — Academy: cartel alta especie 15 s (PRE `bq317tax12`)

- Tras añadir una especie al catálogo, el aviso «Descargando observaciones…» permanece **15 s** en pantalla aunque la especie ya figure en el catálogo o lleguen datos.

## 2026-06-07 — Academy: papelera búsqueda + preview IUCN (PRE `bq317tax11`)

- **Papelera admin:** `canDeleteSpecies()` consulta el catálogo vivo si falta `catalog_source` en la fila de búsqueda; merge enriquece entradas BQ con datos completos del catálogo.
- **Preview especie nueva:** al seleccionar taxón externo carga en paralelo taxon-preview, taxon-brief e IUCN (familia, grupo, badge conservación, foto iNat).
- **Thumbs 404:** no se pide `carousel_0.webp` si no hay thumb local; especies preview usan foto iNat o placeholder 🌿.

## 2026-06-07 — Academy: imports relativos + rutas (PRE `bq317tax10`)

- **ERR_ADDRESS_UNREACHABLE en módulos JS:** todos los `import` absolutos `/pre/...` convertidos a rutas relativas (resuelven desde la URL del módulo, no dependen del host).
- **Import map** de respaldo en `index.html` para cachés antiguas con `/pre/`.
- **PRO → PRE:** `bioquest.yespi.es/` redirige a `/pre/` (evita cargar JS obsoleto de `public/`).
- Eliminado auto-reload agresivo al cambiar versión (podía dejar la app a medias con red inestable).

## 2026-06-07 — Academy: fix búsqueda especies (PRE `bq317tax09`)

- **Búsqueda:** ya no depende de `ensureFfApi()` (bloqueaba resultados si target-species tardaba).
- **Fallback:** si autocomplete no devuelve nada, usa `/academy/inat-search`.
- **Merge:** no descarta resultados sin `inat_id` (Minka); clave por ID o nombre científico.
- **Caché:** auto-reload una vez al detectar nueva versión (`bq20260607v20`).

## 2026-06-07 — Academy: fix alta desde búsqueda (PRE `bq317tax08`)

- **Autocomplete:** el proxy prioriza iNat sobre Minka al deduplicar (catalog-request necesita `taxon_inat_id` de iNaturalist).
- **Búsqueda Academy:** `speciesInBqCatalog()` consulta el catálogo vivo por ID; resuelve ID iNat vía `/academy/inat-search` si el resultado es solo Minka.
- **Alta:** `requestAddToCatalog` usa `auth.api` (POST autenticado) y fuerza modo preview con `in_catalog: false`.

## 2026-06-07 — Academy: sesión + papelera admin (PRE `bq317tax07`)

- **Caché catálogo:** invalidación si falta `catalog_source`; contador de generación evita que el precalentamiento al import sobrescriba datos frescos tras `loadList()`.
- **Sesión iframe FotoFauna:** token por `postMessage` siempre dispara `/auth/me` (no solo en primer login).
- **Admin embebido:** URL del iframe incluye `auth_token` para evitar pantalla «contenido bloqueado» antes del postMessage.
- **Listbox especies:** icono 🗑 visible para admins en filas `catalog_source=user` (p. ej. *Muraena helena*) sin necesidad de recargar.

## 2026-06-05 — Academy: alta/baja especies usuario (PRE `bq317tax06`)

- **Alta por usuario:** al confirmar añadir especie, bootstrap guarda ficha IA en `academy_ai_text`, descarga thumbnails locales y clasifica taxonómicamente (grupo + emoji + familia).
- **Baja admin:** `DELETE /academy/catalog-extra?taxon_inat_id=` (requiere `is_admin`) elimina obs, ficha IA, caché y carpeta de thumbs. Icono 🗑 en buscador Academy para admins en especies `catalog_source=user`.
- **Panel admin:** pestaña Academy documenta especies añadidas por usuarios; ficha de usuario detalla privilegios admin.

## 2026-06-03 (tarde) — Rollback rebuild PRE

El rediseño modular (4 ejes) se **revirtió** por decisión del usuario: UX no satisfactoria y regresiones percibidas.

- **Restaurado** `public-pre/` desde `public-pre-legacy-20260602` (copia previa al rebuild, equivalente al backup `bioquest.tar.gz` del 2026-06-02).
- **Archivado** el intento de rebuild en `public-pre-rebuild-20260603-aborted/` (no borrado).
- `version.json` vuelve a `bq20260602pro`. Shell legacy: Academy + FaunaDex + Medallas (hash `#academy`, `#explore`, etc.).

## 2026-06-03 (madrugada) — Rebuild PRE (revertido)

### Rebuild PRE — esqueleto modular nuevo (3 ejes + Medallas) — **NO EN USO**
Reglas: `REBUILD_PRE_REGlas.md`. Contrato: `REBUILD_PRE_ARQUITECTURA.md`. Sin tocar datos ni rutas backend.

- **Legacy archivado** en `webapp/public-pre-legacy-20260602/` (no borrado). Backup previo verificado (`bq-ff-pre-rebuild-20260602-203719`, con `SHA256SUMS`).
- **Shell fino nuevo** (`index.html` 265 líneas, antes ~2.260 monolíticas): nav de 4 ejes **Especies · Mapa · Conservación · Medallas**, router por hash (`core/router.js`, con alias de hashes legacy), login modal + restauración de sesión **silenciosa** (sin rebote SSO para anónimos en los 3 ejes públicos), drawer de usuario.
- **CSS extraído** a `styles/theme.css` (tokens dark-first + componentes comunes). Soporte modo claro (`html.bq-light`).
- **Procedencia de datos visible** (`core/provenance.js` + `<provenance-bar>`): footer permanente «N {unidad} · Fuente: … · enlaces a Minka/iNaturalist». Sin marketing.
- **Composables reusados** del legacy (useAuth, useFilters, useBbox, useLocation…) vía `provide`/`inject`.
- **Vistas nuevas** (modulares, una por fichero):
  - `EspeciesView.js` — catálogo de 100 especies por grupo + buscador; ficha con stats, IUCN, fotos (con atribución + enlace a la observación), resumen IA (con aviso) y tendencia. (`/academy/target-species|species-stats|species-rich|species-photos|species-doc|iucn|year-trend`).
  - `MapaView.js` — distribución sobre 223k obs: estilos Calor/Puntos/Celdas + Migración (fenología, no tracks) + animación temporal mensual. (`/academy/data-extent|heatmap|timeseries|timeseries-group|migration-pattern|effort-stats`).
  - `ConservacionView.js` — áreas WDPA (4020) con ficha IA al clic, capas buques investigación y esfuerzo pesquero GFW, paneles Invasoras e IUCN. (`/academy/conservation-areas|conservation-brief|research-vessels|fishing-effort|invasive|invasive/yearly|iucn-ranking`).
  - `MedallasView.js` — resumen + 2 gráficas canvas (evolución/actividad) + medallas (auth). (`/awards/mine|history`, `/sync/status/me`).
- **Fix**: `conservation-areas` se llamaba con `limit=600` (el backend valida ≤50 → 422). Corregido a 50.
- **QA**: nuevos audits Playwright `/mnt/scripts/fauna/audit-bq-rebuild-smoke.mjs` (4 ejes montan sin errores JS) y `audit-bq-rebuild-func.mjs` (renderizan contenido real). Capturas en `/mnt/docs/bioquest/rebuild-shots/`. Todo verde con Chrome del sistema.
- **Pendiente**: pestaña Fotos (galería con más imágenes), pruebas de la animación temporal y migración en profundidad, pulido móvil, y **deploy a PRO solo con permiso explícito**. `version.json` → `bq20260603rebuild`.

## 2026-05-27

### Academy — implementación inicial
- Nuevo endpoint backend `/academy/year-trend` para tendencia anual
- Catálogo ampliado: 46 especies icónicas (elasmobranquios, nudibranquios, tortugas, cetáceos, mamíferos terrestres, medusas, invasoras, aves, peces)
- Tabla `public_observations` creada en BD fauna con índices por taxon, mes, año y coordenadas
- Script de descarga masiva `bq_download_observations.py` — usa `taxon_name` (Minka no usa IDs de iNaturalist)
- `AcademyView.js` implementado: tarjetas por grupo, detalle con stats, gráfico mensual de barras, tendencia anual
- CSS completo para Academy en `index.html`
- `routes_academy.py` refactorizado: catálogo con emoji y group_label, fallback Minka live

### FaunaDex — multiselect taxonómico
- `TAXON_GROUPS` definidos en `useFilters.js` (12 grupos: Reptilia, Aves, Mammalia, Actinopterygii, Elasmobranch, Mollusca, Insecta, Arachnida, Plantae, Fungi, Chromista, Amphibia)
- `filterTaxa` añadido a `useFilters` con `toggleTaxon()` y serialización como `iconic` para Minka
- Dropdown multiselect con checkboxes en barra de filtros de FaunaDex
- Cierre con overlay transparente al hacer click fuera

### Navegación
- Orden de tabs cambiado: Academy → FaunaDex → Medallas
- Bug fix: `const location` renombrado a `const locComposable` — colisionaba con `window.location` en la zona TDZ causando ReferenceError al cargar
- Barra de filtros alineada: `padding-left: 200px` para quedar bajo los tabs (no bajo el logo)

### FotoFauna
- Botón BioQuest `🧬 BQ` añadido al header de `index-mobile.html` junto al avatar de usuario

## 2026-05-26

### BioQuest — restructuración nav
- Nav de 2 filas: fila 1 (logo + tabs + usuario), fila 2 (filtros)
- Logo `bq-logo-nav.jpg` recortado a 92px de alto mostrando nudibranch + texto BioQuest
- Logo overlay hover con blur (`bq-logo-grande.jpg`)
- Filtros en FaunaDex y Academy únicamente (no en Medallas)
- Composables compartidos: `useFilters`, `useBbox`, `useLocation` instanciados en `index.html` y pasados como props a ExploreView

### BioQuest — correcciones anteriores
- Badge "Buen ojo" y otros: `v-if` → `v-show` para evitar destrucción de DOM
- `evaluateBadges` añadido a `onBboxChange` (no se llamaba nunca)
- Admin abre como drawer iframe (no nueva pestaña)
- Menú usuario como drawer lateral estilo FotoFauna
- Marcadores del mapa: más grandes, texto en negro

### FotoFauna — mejoras
- Logo hover con overlay blur en `index-mobile.html`
- Botón BioQuest en menú usuario (amarillo, abre nueva pestaña)
- AutoID y Pokédex eliminados del menú usuario (quedan en nav)
