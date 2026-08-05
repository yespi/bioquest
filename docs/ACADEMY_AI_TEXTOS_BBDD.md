# Academy — textos IA en base de datos

## Almacenamiento

Tabla PostgreSQL `academy_ai_text`:

| kind | ref_key | Contenido |
|------|---------|-----------|
| `species_doc` | `inat_id` | Resumen narrativo |
| `species_rich` | `inat_id` | Ficha JSON |
| `chart_help` | modo gráfico | Ayuda voz |
| `conservation_brief` | site_id WDPA | Área protegida |

La API lee BBDD primero; solo genera con LLM si falta fila.

## Precalentado (diario / semanal)

```bash
python3 /mnt/scripts/fauna/bq-ai-warm.py --only-missing --delay 1.5
```

Cron sugerido: `15 3 * * *` → `bq-ai-warm-cron.sh`  
Incluido en `bq-academy-weekly.sh` (domingo).

## Revisión semanal

Pulido conservador (sin inventar datos), ~20 filas por semana:

```bash
python3 /mnt/scripts/fauna/bq-ai-revise-weekly.py --limit 20
```

Endpoint: `POST /academy/internal/ai-revise-batch` (solo `127.0.0.1`).

Requiere `fauna_api` reiniciado tras desplegar cambios en `routes_academy.py`.

## Endpoints internos (localhost)

- `POST /academy/internal/ai-warm-batch` — precalienta catálogo completo
- `POST /academy/internal/ai-revise-batch` — lote revisión

## Estadísticas precalculadas (mapas y gráficos)

Tablas `academy_taxon_cache` (por especie) y `academy_global_cache` (extent, effort).  
Script: `bq_recompute_academy_cache.py` / cron `bq-recompute-academy-cache.sh`.  
Detalle: [`PERFORMANCE_ACADEMY_FF.md`](PERFORMANCE_ACADEMY_FF.md).
