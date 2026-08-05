# Buques con función ecológica — Catálogo y capas mapa

> **Estado actual:** 14 entradas en `research_vessels_seed.json` (v2, campo `category`); sync seed fallback cubre los 14 sin AIS en vivo.  
> **Código:** `conservation_layer_class.py`, `conservationLayerTypes.js`, `bq_research_vessels_sync.py`

---

## Taxonomía objetivo (colores mapa)

| Capa ID | Color UI | Descripción | Ejemplos |
|---------|----------|-------------|----------|
| `vessels_es` | **Azul** `#2563eb` | Instituciones españolas (IEO, CSIC, Armada, universidades) | Ramón Margalef, Ángeles Alvariño, García del Cid |
| `vessels_ngo` | **Naranja** `#f97316` | ONG, campañas, vigilancia oceánica | Greenpeace, Sea Shepherd, Oceana |
| `vessels_foreign` | **Morado** `#9333ea` | Investigación oceanográfica internacional | G.O. Sars, Pourquoi Pas?, Pelagia |
| `vessels_other` | **Gris** `#64748b` | Ciencia ciudadana, velero documental, auxiliar no clasificado | Pendiente inventario |

**Hoy en código:** solo `vessels_ocean` (índigo) y `vessels_ngo` (naranja). Los buques extranjeros del seed se pintan como `vessels_ocean`. **Tarea:** ampliar `classify_vessel_category()` y `LAYER_COLORS`.

---

## Fuentes de datos

1. **Semilla manual** — `backend/data/research_vessels_seed.json` (MMSI/IMO, `last_known_*` si AIS falla).
2. **AISStream** — job `bq_research_vessels_sync.py` → tabla `research_vessel_positions`.
3. **Ampliación pendiente** — inventario público IEO/CSIC, Ifremer, EMSO, barcos ONG activos en Mediterráneo/Atlántico ibérico.

---

## Catálogo a ampliar (prioridad)

### España (azul)
- Flota IEO completa (incl. embarcaciones menores regionales)
- CSIC + unidades universitarias (UIB, USC, UPV…)
- Guardia Civil / Salvamento con misiones científicas (si MMSI público)

### ONG (naranja)
- Campañas activas Mediterráneo
- Embarcaciones de limpieza o monitoreo cetáceos

### Internacional (morado)
- Ya en seed: Noruega, Francia, Países Bajos, Portugal
- Pendiente: UK, Italia, Alemania (campaigns en aguas españolas)

### Nota sobre enlaces externos

Páginas como [yespi.es/w/cargaev](https://yespi.es/w/cargaev) son **trackers web independientes** (multi.yespi.es), no entradas AIS. Enlazar desde **Academy → Organizaciones**, no del layer de buques.

---

## Comandos

```bash
# Sync posiciones AIS
docker exec fauna_api python3 /app/bq_research_vessels_sync.py

# Ver posiciones en BD
docker exec postgres-global psql -U admin_yespi -d fauna -c \
  "SELECT name, operator, lat, lng, observed_at FROM research_vessel_positions ORDER BY observed_at DESC LIMIT 20;"
```

---

## Tareas técnicas

1. Añadir campo `category` en seed (`es` | `ngo` | `foreign` | `other`).
2. Extender `classify_vessel_category()` para leer seed/operator país.
3. Actualizar `conservationLayerTypes.js` + leyenda mapa + CSS swatches.
4. Script `bq_vessels_seed_expand.py` — borrador desde listas públicas (sin deploy UI hasta validar).
