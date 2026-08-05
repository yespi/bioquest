# FotoFauna + BioQuest — 4 orquestadores

Un solo modelo de cron para FF y BQ. Los pasos hijo **no llevan cron propio**.

## Crontab recomendado (plataforma Fauna)

```cron
# --- Plataforma FF+BQ (4 líneas) ---
0  1 * * * /mnt/scripts/fauna/fauna-nightly.sh >> /home/yespi/logs/fauna-nightly.log 2>&1
0  2 * * * /mnt/scripts/fauna/fauna-media-nightly.sh >> /home/yespi/logs/fauna-media.log 2>&1
30 1 * * 0 /mnt/scripts/fauna/fauna-weekly.sh >> /home/yespi/logs/fauna-weekly.log 2>&1
20 5 * * * /mnt/scripts/fauna/fauna-users-daily.sh >> /home/yespi/logs/fauna-users-daily.log 2>&1
```

**Thumbs Academy** (paso `bq-academy-media-nightly.sh`, dentro de `fauna-media-nightly` 02:00),
fases secuenciales (NO paralelas, para no sobrecargar iNat):
1. `download` — carrusel/categorías del catálogo Atlas (`TARGET_SPECIES`).
2. `rotate` — renueva fotos antiguas del Atlas.
3. `precache-usuarios` (`bq_precache_all_thumbs.py`) — **~1836 taxa** distintos en
   `user_observations`: solo `carousel_0.webp` (thumbnail). Catálogo Atlas: hasta **10** slots
   carrusel + categorías. `only_missing` rellena huecos sin saltar la especie entera.
   On-demand: `GET /species-photos?wait_precache=1` descarga a disco antes de responder.

**Backup integral** (independiente, no forma parte de estos scripts):

```cron
0 3 * * * ./docker/backups/backup_maestro.sh >> ./docker/backups/logs/backup_maestro_cron.log 2>&1
0 4 * * * /bin/bash /mnt/scripts/backup_hansolo_archive.sh >> ./docker/backups/logs/backup_hansolo_cron.log 2>&1
30 4 * * * /mnt/scripts/backup/verify-backup.sh
```

## Los 4 orquestadores

| Script | Hora | Incluye |
|--------|------|---------|
| **`fauna-nightly.sh`** | 01:00 | Token iNat, buques, sync obs catálogo BQ, stats precalculadas, IUCN 1/15 |
| **`fauna-media-nightly.sh`** | 02:00 | WebP Academy (precache + rotación), **edu-media-daily** (documentales + posters), `bq-ai-warm` (textos IA faltantes) |
| **`fauna-weekly.sh`** | Dom 01:30 | WDPA, invasoras, rich-warm, IA revise (Academy), **sync taxones Minka→BD** |
| **`fauna-users-daily.sh`** | 05:20 | Sync Minka usuarios FF, medallas, `calc_badges` |

### Ya no hace falta en crontab

| Antiguo | Motivo |
|---------|--------|
| `renew-inat-token.sh` suelto | Dentro de `fauna-nightly` |
| `bq-academy-nightly` / `daily` / `super-daily` | → `fauna-nightly` |
| `bq-academy-media-nightly` / `thumbs-*` | → `fauna-media-nightly` |
| `bq-academy-weekly` | → `fauna-weekly` |
| `ff_sync_and_badges_daily` | → `fauna-users-daily` |
| `ff_sync_and_badges_midweek` | Redundante (mismo lote) |
| `calc_badges_cron.sh` cada hora | Solo tras sync en `fauna-users-daily` |
| `bq-ai-warm-cron.sh` suelto | Dentro de `fauna-media-nightly` |

## Alias (compatibilidad)

Todos redirigen a los orquestadores `fauna-*` o al paso interno `bq-academy-*`.

## Logs

| Log | Contenido |
|-----|-----------|
| `~/logs/fauna-nightly.log` | Token + datos BQ |
| `~/logs/fauna-media.log` | Fotos + IA warm |
| `~/logs/fauna-weekly.log` | Semanal BQ |
| `~/logs/fauna-users-daily.log` | FF usuarios + badges |
| `~/logs/ff-sync-badges.log` | Detalle sync (interno) |
| `~/logs/seo-daily.log` | SEO-04: rebuild + validate + purge CF (BQ+FF) |

## SEO diario (SEO-04)

```cron
30 4 * * * yespi /mnt/scripts/fauna/seo-daily-maintain.sh >> /home/yespi/logs/seo-daily.log 2>&1
```

Orquesta `seo-rebuild-all.sh`, validación (`seo-validate.mjs`), comprobaciones HTTP y purge Cloudflare.
Visible en el planificador como tarea `seo_daily_maintain` (paused; el cron actualiza `last_result`).

## Pendiente producto

- Carrusel solo `/academy/species-photos` — `CONTINUIDAD_SESION.md`.
