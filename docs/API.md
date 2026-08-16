# BioQuest — Guía de la API

**Última actualización:** 2026-08-16

BioQuest **no tiene backend propio**. El frontend (`bioquest-webapp`, nginx :3012) proxya todas las llamadas API a **`fauna_api`** (FastAPI compartido con FotoFauna).

Referencia completa del backend: [`../../ecosistema-fauna/API.md`](../../ecosistema-fauna/API.md).

---

## Base URL

En producción:

```
https://bioquest.yespi.es/ff-api/
```

Nginx (`bioquest/webapp/nginx.conf`) reescribe:

```
/ff-api/academy/timeseries  →  http://fauna_api:3005/academy/timeseries
```

Desde el navegador BQ usa `credentials: 'include'` (cookies SSO). Desde **scripts externos** usa un **token PAT** (ver abajo).

---

## Autenticación para pruebas externas

### Opción recomendada: PAT de admin

1. Inicia sesión en FotoFauna como administrador.
2. Abre el panel Admin → pestaña **API BQ**.
3. Crea un token con nombre descriptivo (p. ej. «Miquel — prueba Atlas»).
4. Copia el valor `ff_pat_…` (solo visible al crearlo).

Ejemplo:

```bash
export BQ_API="https://bioquest.yespi.es/ff-api"
export FF_PAT="ff_pat_xxxxxxxx"

# Comprobar identidad
curl -sS -H "Authorization: Bearer $FF_PAT" "$BQ_API/auth/me" | jq .

# Serie temporal de una especie (taxon_id iNat)
curl -sS -H "Authorization: Bearer $FF_PAT" \
  "$BQ_API/academy/timeseries?taxon_id=52764&place_id=6793" | jq .
```

### Opción navegador (SSO)

Si el usuario ya está logueado en `*.yespi.es`, las peticiones desde la propia web BQ llevan cookie `ff_refresh` automáticamente (`useAuth.js`, `fetchBqApi`).

No compartir JWT de 1 h entre dominios manualmente; preferir PAT para integraciones.

---

## Endpoints más usados por vista

### Atlas / Academy

| Vista | Rutas típicas |
|-------|----------------|
| Atlas | `/academy/timeseries`, `/academy/heatmap-frame`, `/academy/species-rich`, `/academy/multi-species-trend` |
| Ficha especie | `/academy/species-doc`, `/academy/species-photos`, `/academy/wiki-exists` |
| Conservación | `/academy/conservation/areas`, `/academy/conservation/area/{site_id}` |
| Ranking | `/academy/ranking/*` |

Parámetros comunes: `taxon_id` (iNat), `place_id`, bbox (`swlat`, `swlng`, `nelat`, `nelng`), filtros de fecha/mes.

### FaunaDex / Medallas

| Vista | Rutas |
|-------|-------|
| Mis especies | `/pokedex/species/mine`, `/pokedex/observations` |
| Medallas | `/badges/mine`, `/badges/evaluate` |
| Trofeos | `/trophies/mine` |

Requieren usuario autenticado (JWT, cookie o PAT admin).

### Explore / Buceo

| Vista | Rutas |
|-------|-------|
| Mapa buceo | `/buceo/spots`, `/buceo/spot/{id}`, `/buceo/dive-sites` |
| Meteo | `/buceo/weather`, `/buceo/forecast`, `/buceo/meteo-grid` |
| Webcams | `/buceo/webcams`, `/buceo/webcam-preview?id=…` |

Muchos endpoints de buceo son **públicos** (no exigen auth).

### Academy Edu

Prefijo `/academy/edu/` — cursos, diapos, biblioteca multimedia. Ver [`SCHEMA_ACADEMY_EDU.md`](SCHEMA_ACADEMY_EDU.md).

### Búsqueda global

`GET /search?q=…` — barra de búsqueda unificada (taxa, lugares, cursos).

---

## Sesiones de uso (telemetría)

El frontend registra actividad con `POST /usage/ping` (`session_key` con prefijo `bq_` para BioQuest). No necesario para integraciones externas con PAT.

---

## Límites y buenas prácticas

- PAT: máx. **10** tokens activos por admin; revocar los que no uses.
- No automatizar descargas masivas de fotos (`/academy/photo-proxy`) sin coordinar — impacta cuotas iNat.
- Reintentar ante **502/503/504** (arranque/reinicio de `fauna_api`).
- Para desarrollo local en HanSolo: backend en `:3005`, BQ en `:3012`.

---

## Repos públicos

Copia orientada a colaboradores externos:

- [`yespi/fotofauna`](https://github.com/yespi/fotofauna) → `docs/API.md`
- [`yespi/bioquest`](https://github.com/yespi/bioquest) → `docs/API.md`
