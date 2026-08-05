# BioQuest / FotoFauna — rendimiento y lentitud intermitente

> **Síntoma típico:** en LAN, fallos puntuales que al recargar “vuelven”; todo muy lento un rato.
> **Causas frecuentes:** DNS inestable (AdGuard), muchas peticiones en paralelo, APIs externas (iNat/GFW/IA), carga nocturna en PostgreSQL.

## Thumbnails WebP (precache nocturno, 2026-06-04)

Descarga en **oleadas** sin saturar iNaturalist (~2 especies/oleada, 2,5 s entre peticiones iNat, 45 s entre especies, ventana 6 h).

| Pieza | Ruta |
|-------|------|
| Ficheros | `./docker/ecosistema-fauna/academy-thumbs/{inat_id}/*.webp` + `manifest.json` |
| Script | `bq_download_academy_thumbs.py` / `bq-academy-thumbs-nightly.sh` |
| Cron | `0 2 * * *` (tras sync 01:00) |
| API | `GET /academy/species-photos` → URLs locales; `GET /academy/thumbs/{id}/{file}.webp` |

Env: `ACADEMY_THUMB_MAX_PX=640`, `ACADEMY_THUMB_WEBP_QUALITY=88` (prioridad calidad; ~80–150 MB catálogo), `BQ_THUMBS_WAVE_SIZE=2`.

```bash
# Una especie manual
docker exec -w /app fauna_api python3 /app/bq_download_academy_thumbs.py --taxon-id 42407
# Toda la noche
/mnt/scripts/fauna/bq-academy-thumbs-nightly.sh
```

Estimación: 103 especies × ~12 imágenes × ~2,5 s ≈ **45–90 min** de peticiones iNat + descargas S3; con pausas **varias horas** (diseñado).

### Orden aleatorio y rotación diaria (2026-06-05)

- API `species-photos`: listas barajadas con semilla **fecha + taxon_id** (misma UX todo el día, distinta cada día).
- Cron **`30 3 * * *`**: `bq-academy-thumbs-rotate-daily.sh` — reemplaza 1 WebP de carrusel por especie (obs iNat recientes).
- Orquestación: [`ACADEMY_CRONS.md`](FAUNA_CRONS.md) (3 scripts: nightly, media-nightly, weekly).

```bash
# Probar rotación en una especie
docker exec -w /app fauna_api python3 /app/bq_rotate_academy_thumbs.py --taxon-id 42407
```

### Pendiente frontend (cuando el precache termine)

- **Quitar fetch directo a iNat** en `AcademyView.js` → `_loadPhotos()`; usar solo `/academy/species-photos` (URLs `/academy/thumbs/...`).
- Criterio listo: `ls ./docker/ecosistema-fauna/academy-thumbs | wc -l` ≈ 103.
- Detalle: [`CONTINUIDAD_SESION.md`](../../_archive/historico/CONTINUIDAD_SESION.md) § «Pendiente — fotos Academy solo locales».

## Tablas precalculadas (2026-06-04)

Tras cada descarga nocturna de observaciones y en el lote diario:

| Tabla | Contenido |
|-------|-----------|
| `academy_taxon_cache` | Por especie: totales, mes/año, `source_trend`, migración, timeseries (all/inat/minka), heatmap, extent |
| `academy_global_cache` | `data_extent`, `effort_stats` |

Scripts: `bq_recompute_academy_cache.py`, `bq-recompute-academy-cache.sh` (paso en `bq-academy-daily.sh`).  
La API devuelve `"cached": true` cuando sirve desde estas tablas.

```bash
docker exec -w /app fauna_api python3 /app/bq_recompute_academy_cache.py --all
docker exec -w /app fauna_api python3 /app/bq_recompute_academy_cache.py --taxon-id 42407
```

## Ya cubierto (no repetir como única palanca)

| Medida | Efecto |
|--------|--------|
| Descarga obs catálogo + usuario + invasoras | Mapas y stats desde BD local (rápido si hay filas) |
| `bq-ai-warm` + `academy_ai_text` | Fichas `species-doc` / `species-rich` / `chart-help` sin esperar LLM |
| AdGuard perfil `fastest_addr` | Menos timeouts DNS en LAN |

## Cuello de botella medidos (HanSolo, 2026-06-04)

| Endpoint / acción | Tiempo aprox. | Notas |
|-------------------|---------------|--------|
| `GET /academy/target-species` | ~150 ms → **&lt;20 ms** tras 1× `GROUP BY` | Antes: 103× `COUNT(*)` |
| `GET /academy/data-extent` | ~0,8 s | Percentiles sobre todas las obs |
| `GET /academy/timeseries` | ~0,2 s | Depende de nº de puntos |
| `GET /academy/species-photos` | **~3 s** | 7 llamadas iNat en paralelo |
| Carousel Academy (navegador) | hasta 8 s | `fetch` directo a `api.inaturalist.org` (duplica trabajo) |
| `species-doc` sin warm | 10–30 s | LLM Groq/OpenRouter |
| Panel Conservación / GFW | 30–60 s | Tiles y áreas WDPA |

Ping a Google ~20–35 ms es **WAN**, no DNS de la app.

## Mejoras recomendadas (prioridad)

### P0 — Impacto alto, poco riesgo

1. **`target-species` en una sola query** — implementado en `routes_academy.py` (GROUP BY).
2. **Dejar de llamar iNat desde el navegador** en `AcademyView.js` (carousel): usar solo `/academy/species-photos` (ya existe en backend).
3. **Precache fotos iNat** — tabla `academy_species_photos` (JSON) + script `bq-photos-warm.py` en cron semanal (mismas 7 categorías que `species-photos`).
4. **Ampliar `bq-ai-warm.py`** — comprobar que todas las especies tienen `species_rich` + `species_doc` + todos los `chart_help` (modos: heat, hex, cluster, timeline, bars, monthly, trend…).
5. **DNS estable** — watchdog + perfil AdGuard; si vuelve la lentitud “de red”, revisar `~/logs/internet-watchdog.log`.

### P1 — Backend / BD

6. **Tabla agregada `academy_taxon_stats`** — precalcular por `taxon_id`: total, min/max año, `by_month[]`; refresco en `bq-academy-daily` tras sync. Endpoints `species-stats` / `year-trend` leen la tabla (evitan varios `GROUP BY` en caliente).
7. **`data-extent` precalculado** — una fila en `academy_meta` actualizada de noche.
8. **Caché HTTP** en nginx para `GET /academy/target-species`, `data-extent` (TTL 5–15 min, solo lectura).
9. **Cola / semáforo LLM** en `fauna_api` — máx. 1 generación IA concurrente; el resto sirve stale o 503 con retry (evita bloquear el worker con 5 usuarios).

### P2 — Frontend

10. **Cargar Panel C (invasoras, ranking, WDPA) solo al abrir la pestaña** — no en el mount inicial de Academy.
11. **`AbortController` ya usado en species** — extender a conservation / fishing-effort.
12. **Service Worker / cache bust** — `version.json` ya existe; asegurar `bq2026…` en assets tras cada deploy PRE.

### P3 — Operación HanSolo

13. **Ventana nocturna sync** — `bq-academy-sync-nightly` (01:00, hasta 5 h) compite con PostgreSQL; si usas la app de madrugada, valorar `BQ_NIGHTLY_MAX_RUNTIME_SEC` más corto o horario 02:00–05:00.
14. **`calc_badges_cron` cada 15 min** — subir a `0 * * * *` si la CPU/IO picotea en FaunaDex.
15. **Playwright smoke cada hora** — no afecta usuarios; el deep audit sí (manual).

## Benchmark 10× cargas (2026-06-04, Playwright LAN)

Script: `/mnt/scripts/fauna/benchmark-load-10x.mjs` → `/home/yespi/logs/load-benchmark-10x.json`

| Ruta | Nav p50 (load + espera) | APIs más lentas (p90) |
|------|-------------------------|------------------------|
| BioQuest `#academy` | ~6,4 s | `species-photos` **1,5 s**; iNat navegador **0,8 s**; `timeseries`/stats **~0,3 s** |
| BioQuest `#explore` | ~8,3 s | Solo estáticos (~60 ms); APIs tras login/mapa |
| FotoFauna `/pre/` | ~6,8 s | Muchos JS composables ~150 ms; sin picos API en anónimo |
| FotoFauna `autoid.html` | ~6,1 s | `/admin/autoid/stats` ~50 ms |

Vía túnel (`curl` bioquest.yespi.es): `target-species` / `data-extent` / `timeseries` / `species-rich` **~10–25 ms** con precache; `species-photos` **~6 s** la 1.ª vez, **~11 ms** en caché RAM del proceso.

## Checklist rápido cuando “va lento otra vez”

```bash
# APIs
for ep in target-species data-extent species-photos?taxon_inat_id=42407; do
  curl -s -o /dev/null -w "$ep %{time_total}s\n" "http://127.0.0.1:3005/academy/$ep"
done

# DNS
dig @127.0.0.1 bioquest.yespi.es +stats | grep 'Query time'
tail -20 ~/logs/internet-watchdog.log

# Carga
docker stats --no-stream fauna_api postgres-global
tail -30 ~/logs/bq-academy-sync-nightly.log
```

## Relacionado

- [`ACADEMY_AI_TEXTOS_BBDD.md`](ACADEMY_AI_TEXTOS_BBDD.md)
- [`/mnt/docs/sistema/HANSOLO_CRON_Y_SERVICIOS.md`](/mnt/docs/sistema/HANSOLO_CRON_Y_SERVICIOS.md)
- [`CONTINUIDAD_SESION.md`](../../_archive/historico/CONTINUIDAD_SESION.md)
