# Explore (Buceo) — Capas de atmósfera

**Última actualización:** 2026-07-30 (~22:15, Playwright overhaul temp/sat/meteo)  
**Código:** `./docker/bioquest/webapp/public-pre/views/BuceoView.js`  
**Tint / nubes / calor:** `./docker/bioquest/webapp/public-pre/composables/bq-meteo-tint.js`  
**Satélite meteo:** `./docker/bioquest/webapp/public-pre/composables/bq-meteo-sat.js`  
**Viento:** `./docker/bioquest/webapp/public-pre/composables/bq-wind-particles.js`  
**API:** `GET /ff-api/buceo/meteo-grid`, `GET /ff-api/buceo/wind-arrows` (`ecosistema-fauna/backend/routes_buceo.py`)  
**PRO:** `https://bioquest.yespi.es/` → pestaña Explore (`#buceo`)  
**Build:** `bq71724b3a`  
**Evidence:** `/mnt/docs/bq-playwright-evidence/` + probe `public-pre/tests/explore-atm-playwright-probe.mjs`

---

## 1. Qué es

En el mapa Explore el usuario elige **una** capa de atmósfera (radio exclusivo). Por defecto: **Meteo** (nubes animadas). Las barras de idoneidad en los spots siguen visibles en todas las capas.

## 2. Las cuatro capas

| Modo (`atmMode`) | UI | Qué muestra | Fuente de datos |
|------------------|----|-------------|-----------------|
| **`meteo`** (default) | «Meteo» | Nubes + wash; status «cielo despejado / poca nubosidad» si cloud_cover bajo | `meteo-grid` → `cloud` |
| **`temp`** | «Temperatura» | Heat Windy con **contraste local** + °C + fronteras + viento; chip °C al cursor | `meteo-grid` → `temp`; `wind-arrows` |
| **`wind`** | «Viento» | Color por velocidad km/h + partículas | `wind-arrows` |
| **`sat`** | «Satélite» | **MTG Dust RGB** animado + play/pausa + scrubber (~2 h) | `mtg_fd:rgb_dust`; fallback `mtg_fd:ir105_hrfi` |

## 3. Hallazgos Playwright (2026-07-30) y fixes

### Antes (`bq2e0ba5a7`)
- **Temp:** heat/labels/borders OK; viento a veces tardaba (host `display:none` hasta cargar).
- **Sat:** scrub cambiaba el **timestamp** pero `setParams({time})` **no pedía nuevos GetMap** → frames visualmente idénticos. Además IR10.5 en Med despejado tenía diff pixel ~0 entre frames.
- **Meteo:** capa **viva** (canvas>0, fetch OK) pero mean cloud ~8% → se veía «vacía»; no era bug 0×0.

### Después (`bq71724b3a`)
- Sat: **rebuild de capa WMS por frame** (no `setParams`); producto **Dust RGB** (diff frame ~7.5); CSS `--rgb` sin `screen`.
- Temp: estirado local de color + hover °C + wind watchdog; labels más densas.
- Meteo: wash mínimo con cielo claro + texto `Cielo despejado · nubosidad media X%`.
- Probe PRO: **24 PASS / 0 FAIL**.

## 4. Comportamiento técnico

### 4.1 Satélite (`bq-meteo-sat.js`)
- `applyFrame` → `rebuildActiveLayer()` siempre (rompe caché Leaflet/WMS).
- `window.__bqMeteoSat` para diagnóstico.
- Scrub de usuario ignora `zoomBusy`.

### 4.2 Tint (`bq-meteo-tint.js`)
- `tempLocalRange` + mezcla 35% absoluto / 65% estirado.
- `summarizeCloudCover()` → status UX.
- `sampleTempAtContainerPoint` para hover.

### 4.3 Zoom safety
Sin cambios: `safeLeaflet.js` evita `getCenter` null.

## 5. Deploy

```bash
bash ./docker/bioquest/deploy-to-pro.sh
# Probe:
cd ./docker/bioquest/webapp/public-pre/tests
BQ_URL=https://bioquest.yespi.es/ BQ_PROBE_OUT=/tmp/bq-playwright node explore-atm-playwright-probe.mjs
```

## 6. Verificación rápida

1. Explore → **Satélite**: Dust RGB colorido; scrub cambia imagen (no solo reloj).
2. **Temperatura**: calor contrastado + °C + fronteras + flechas; cursor muestra °C.
3. **Meteo** con cielo claro: mensaje de nubosidad media; partículas tenues (no inventa nubes).
4. Zoom +/- sin crash `getCenter`.
