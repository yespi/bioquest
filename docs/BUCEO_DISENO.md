# Buceo / Excursiones — Diseño (DIVE-01)

> Nueva sección de **BioQuest** para localizar **dónde ir a observar fauna** (bucear/snorkel en el mar; rutas/reservas en tierra) y conseguir nuevas observaciones. Reusa la infra de BQ (mapa `useMap`, Minka, JWT, POIs, doc-IA). **No** es web aparte ni va en FotoFauna.
>
> Estado: **PRO** desde 2026-07-04 (`bq2a05720e`). Implementación validada en PRE; promovida a producción con gates.

## 1. Objetivo y filosofía

Gustavo quiere una herramienta que responda a *"¿dónde voy hoy a ver fauna?"*. Diferencial frente a Windy/apps de buceo: no mostramos datos meteo crudos, mostramos **decisiones** (semáforo + dado) y lo cruzamos con **qué especies puedes sumar a tu FaunaDex**. Es BioQuest puro (gamificación + ciencia ciudadana), no un portal náutico.

Aplicando los 5 pasos: el motor de spots se diseña **agnóstico de hábitat** desde el día 1 (un solo motor, no dos). **Buceo** = subconjunto marino; **Excursiones** = subconjunto terrestre. Si más adelante no usamos lo terrestre, se quita; pero el coste de contemplarlo ahora (un campo `habitat`) es casi nulo y evita reescribir el motor.

## 2. Motor agnóstico mar/tierra

Tabla única `spots` (campo `habitat: 'sea' | 'land'`). Buceo filtra `habitat='sea'`; Excursiones `habitat='land'`. Toda la lógica (semilla, dedup, semáforo, dado, capas) comparte código; solo cambian:

| Aspecto | `sea` (Buceo) | `land` (Excursiones) |
|---------|---------------|----------------------|
| Snap | a mar (`snap_to_sea`) | a tierra (inverso) |
| Semáforo | oleaje/viento/periodo (Open-Meteo **Marine**) | lluvia/viento/temp (Open-Meteo estándar) |
| Validación | Náyade/MITECO, FECDAS, EMODnet (batimetría) | RENPA, Natura 2000, parques (ya tenemos `conservation_areas`) |
| Especies | grupos marinos del catálogo | grupos terrestres (terrestre, ave, anfibio, reptil, insecto) |

## 3. Datos disponibles (investigado 2026-06-25)

- **`public_observations`**: 452.539 obs (190.744 en España). **NO tiene columna de usuario/observador** → la semilla **no** puede ser "top-40 users" desde esta tabla; será por **clustering geográfico** de las obs. Tampoco tiene `depth_m` poblado (la profundidad vendrá de batimetría externa).
- **256.367 obs de especies marinas** (cruce `public_observations` × `academy_catalog_core` por grupo marino) → base sólida para clustering de spots de buceo.
- **`conservation_areas`** (WDPA): áreas protegidas con `centroid_lat/lng`, `realm` (marino/terrestre), `iucn_cat`, `area_km2`. **Reutilizable** para validar spots y para Excursiones (reservas recomendadas). Ya lo usa el buscador.
- **`snap_to_sea.py`**: existe; usa `geodata/ne_110m_land.shp` (Natural Earth 110m, baja resolución). **Falta el módulo `shapefile` (pyshp)** en el contenedor → instalar. Para precisión costera real conviene un shapefile de mayor resolución (Natural Earth 10m o GSHHG) — decisión de DIVE-02.

## 4. Semilla de spots (DIVE-02)

1. **Clustering** (DBSCAN sobre lat/lng) de las 256k obs marinas (España). DBSCAN, **no media aritmética** (la media de un cluster costero cae a tierra). `eps` ~150–300 m, `min_samples` ~5–10 → cada cluster denso = un punto de buceo real.
2. **Snap a mar** del centroide del cluster (`snap_to_sea`) para corregir obs con coords en tierra.
3. **Dedup** por proximidad (fusionar clusters a < ~200 m).
4. Objetivo: ~1000–2000 spots curados (marinos) en la 1ª iteración (toda España).
5. **iNat fuera o muy filtrado**: ruido + geoprivacy (coords ofuscadas → spots falsos). Usar Minka, que es el dato fiable.

## 5. Validación / consenso con BBDD oficiales (DIVE-03)

El cluster Minka da la **ubicación real de buceo**; una BBDD oficial da el **nombre canónico + metadatos** del punto costero más cercano; la **IA hace el consenso** (nombre, descartar si cae lejos de costa, fusionar duplicados). Fuentes a cruzar:

- **Náyade / MITECO** (calidad de aguas de baño): nombre, municipio, coords, calidad del agua.
- **FECDAS + federaciones autonómicas**: puntos de inmersión catalogados.
- **EMODnet / Copernicus Marine**: batimetría → profundidad real del spot.
- **OpenStreetMap**: `natural=beach`, `sport=scuba_diving`, `leisure=marina` para nombres y POIs.
- Terrestre (Excursiones): **`conservation_areas`** (ya en BD) + RENPA / Natura 2000 / parques + OSM.

## 6. UI — pestaña "Buceo" / Explore (DIVE-04 + capas 2026-07-30)

- **Mapa** con rail **Capas** (radios exclusivos; default **Meteo**):
  - **Meteo** — nubes animadas (`bq-meteo-tint` modo `clouds` + `cloud_cover` API).
  - **Temperatura** — tint azul→rojo (°C).
  - **Viento** — color + partículas (`bq-wind-particles`).
  - **Satélite** — base Esri World Imagery.
- Doc detallada: [`EXPLORE_CAPAS.md`](EXPLORE_CAPAS.md).
- **Idoneidad** (semáforo en spots) independiente de la capa de atmósfera.
- **Informe SEO** en menú usuario solo para admin (`contact@yespi.es`).
- **🎲 Dado** (diseño original): sugerir cala al azar — ver fases; no bloquea las capas.

## 7. Semáforo meteo (DIVE-05)

- **Open-Meteo Marine** (gratis, sin clave: altura de ola, periodo, viento). El buceador **no ve datos meteo**, ve un **semáforo 🟢🟡🔴** calculado por orientación/exposición de la cala. Excursiones usa Open-Meteo estándar (lluvia/viento/temp).

## 8. Fases (resumen, ver `TAREAS_PENDIENTES.md` DIVE-01…07)

1. **DIVE-01** (este doc).
2. **DIVE-02**: script semilla de spots (clustering + snap + dedup), agnóstico hábitat.
3. **DIVE-03**: validación/consenso con BBDD oficiales + IA.
4. **DIVE-04**: UI pestaña (mapa + capas + dado).
5. **DIVE-05**: semáforo Open-Meteo.
6. **DIVE-06**: centros de buceo (2ª fase, directorio que envejece — no bloquea lanzamiento).
7. **DIVE-07**: Excursiones terrestres (mismo motor, `habitat='land'`).

## 9. Decisiones abiertas (consultar a Gustavo cuando proceda)

- Resolución del shapefile de costa para `snap_to_sea` (110m actual vs 10m/GSHHG).
- Umbrales DBSCAN (`eps`, `min_samples`) → calibrar con datos reales en DIVE-02.
- Si el lanzamiento incluye Excursiones terrestres desde el día 1 o solo Buceo (motor preparado para ambos en cualquier caso).
