# Academy — Mapas y visualizaciones científicas

**Última actualización:** 2026-06-01  
**Contexto:** propuestas de rigor ecológico, cruce con tráfico marítimo, evaluación de utilidad.

---

## Lo que ya tenemos (y qué es científicamente)

| Capacidad actual | Equivalente propuesto | Notas |
|------------------|----------------------|-------|
| Mapa **calor** (Leaflet.heat) | KDE / densidad | Aproximación KDE con kernel gaussiano; no calcula percentil 50 (core area) pero evita overplotting |
| Mapa **hex** (celdas por zoom) | Grid occupancy / H3 | Estilo GBIF; abundancia por celda, no H3 geodesic pero mismo concepto |
| Modo **migración** (centroides mensuales + polyline) | Trayectoria temporal agregada | **No** es track de individuo — es fenología espacial de la población |
| Animación mensual + scrubber | Ventana temporal | Estándar para fenología; en reposo mapa vacío hasta interactuar |
| Panel **invasoras** + animación grupo | Expansión temporal | Falta capa mapa «celdas nuevas por año» (prioridad alta) |
| Gráfica **latitud N→S** | Nicho latitudinal | Histograma de abundancia por grado |
| Curva **acumulada** (panel, no mapa) | Tendencia temporal | Útil en panel; el mapa acumulado era redundante |

---

## Evaluación de propuestas

### A. Zona de actividad e invasoras

| Propuesta | Utilidad | Viabilidad con datos actuales | Decisión |
|-----------|----------|-------------------------------|----------|
| **KDE con core area (p50)** | Alta | Media — requiere backend o lib KDE en cliente | **Fase 2** — contorno p50 sobre heat existente |
| **Grid occupancy / H3** | Alta | Alta — hex ya implementado | **Hecho** (hex); mejorar etiquetado «ocupación» |
| **Expansión invasoras por celda/año** | Muy alta | Alta — `animFrames` tienen año | **Fase 1** — modo mapa `expansion` |
| **Presencia/ausencia binaria** | Media | Alta | Toggle en hex (abundancia vs ocupada sí/no) |

### B. Migraciones y movimientos

| Propuesta | Utilidad | Viabilidad | Decisión |
|-----------|----------|------------|----------|
| **Tracks temporales por estación** | Alta | Alta — centroides mensuales ya existen | **Fase 1** — colores estación en polyline |
| **NSD (Net Squared Displacement)** | Alta en telemetría | **Baja** — iNat/Minka no dan ID de individuo reidentificado | **Descartado** salvo datos GPS etiquetados |
| **Chord / Sankey entre regiones** | Alta para aves/cetáceos | Media — agregar backend por cuencas (N/S/E/W Med) | **Fase 2** |
| **Tracks vectoriales por individuo** | Alta | **No** con citizen science agregado | No aplicable |

### C. Contexto ambiental

| Propuesta | Utilidad | Viabilidad | Decisión |
|-----------|----------|------------|----------|
| **Violín temp/salinidad/clorofila** | Muy alta | Media — API Copernicus Marine + muestreo en punto obs | **Fase 3** |
| **Superposición SST/corrientes** | Alta | Media — tiles WMS Copernicus | **Fase 3** |
| **Coocurrencia fauna ↔ barcos** | Muy alta (marino) | Media — ver sección AIS abajo | **Fase 2–3** |

### D. Buques oceanográficos / ONG

| Fuente | Uso en Academy | Almacenar en BBDD |
|--------|----------------|-------------------|
| **Copernicus Marine** | Capas ambientales (SST, clorofila) | No — tiles/API bajo demanda |
| **Eurofleets / UTM-CSIC** | Referencia campañas RV españolas | Opcional metadatos campaña |
| **AIS vía Global Fishing Watch** | Capa **esfuerzo pesquero** (tiles) | **No** — proxy cache |
| **AISHub** (opcional) | Última posición buques oceanográficos (BQ-036) | Sí — tabla `research_vessel_positions` |

---

## Sustitución del mapa «Acumulado total»

**Problema:** mostrar todos los puntos a la vez no aporta más que pausar la animación con el filtro completo; confunde con el modo temporal.

**Solución adoptada (PRE):**

- Eliminar **«Acumulado total»** como modo temporal.
- Añadir **«Densidad espacial»**: agrega todas las observaciones del subconjunto filtrado y recomienda vista **hex** (overview) + **calor** (detalle).
- Mantener la **curva acumulada** solo en panel de gráficas (tendencia temporal), no en mapa.

---

## Barcos de pesca en tiempo real (sin BBDD)

### ¿Es posible?

**Sí.** [Global Fishing Watch API](https://globalfishingwatch.org/our-apis/) ofrece:

- Posiciones y eventos de embarcaciones (AIS procesado).
- Capas de **fishing effort** (horas de pesca por celda).
- Autenticación por token; uso investigación/educación permitido con registro.

Alternativa ligera: **vector tiles** o **heatmap GFW** embebida en Leaflet solo cuando el usuario activa la capa.

### ¿Vale la pena?

| Pros | Contras |
|------|---------|
| Contexto ecológico inmediato (fauna marina vs presión pesquera) | Requiere token GFW y cumplir ToS |
| Narrativa educativa fuerte en Academy | AIS no distingue ONG vs pesquero sin filtrar por MMSI/tipo |
| Complementa invasoras/cetáceos | Riesgo de sobrecargar UI si está siempre activo |

**Recomendación:** capa **opcional** «🎣 Esfuerzo pesquero (GFW)» en el menú ⋮, solo visible en:

- especies/grupos **marinos** o **cetáceos**,
- zoom costero/offshore,
- bbox visible (no global).

### ¿Consumirá muchos recursos?

| Recurso | Impacto estimado |
|---------|------------------|
| **BBDD** | Cero si no persistimos |
| **Backend HanSolo** | Bajo — proxy cache 5–15 min por bbox+zoom en Redis/memoria |
| **API GFW** | 1 req al mover mapa (debounce 800 ms) + cuotas diarias según plan |
| **Cliente** | Bajo–medio — GeoJSON acotado al bbox (~centenares de puntos) o tile raster |

No conviene polling global cada N segundos. Patrón: **fetch on pan-end** + cache TTL.

### Implementación propuesta (Fase 2)

1. Registro token GFW en `(configure via environment)` (`GFW_API_TOKEN`).
2. Endpoint `GET /academy/gfw-effort?swlat&swlng&nelat&nelng&days=7` — proxy con cache.
3. Toggle en Academy kebab; capa semitransparente naranja bajo puntos de fauna.
4. Tooltip: «Coocurrencia espacial orientativa — no implica causalidad».

---

## Roadmap priorizado

| Fase | Entrega | Esfuerzo |
|------|---------|----------|
| **1** ✅ | Densidad espacial (reemplaza acumulado mapa); migración por estaciones | Hecho PRE |
| **1b** | Modo mapa **expansión invasoras** (celdas nuevas/año) | 1–2 días |
| **1c** | Hex: toggle abundancia / presencia-ausencia | 0.5 día |
| **2** ✅ | Capa GFW pesca (proxy, sin BBDD) | Hecho |
| **2** ✅ | Capa buques investigación (AISHub + semilla IEO/CSIC/EU) | Hecho BQ-036 |
| **2** | Chord migratorio N↔S por regiones (backend) | 2–3 días |
| **2** | Contorno core area KDE p50 | 1–2 días |
| **3** | Copernicus SST/clorofila + violín nicho | 3–5 días |
| **3** | Mapas áreas protegidas + IMMA/ISRA + ficha IA | 5–8 días — ver [ACADEMY_AREAS_PROTEGIDAS.md](PROPUESTA_AREAS_PROTEGIDAS.md) |
| **—** | NSD, tracks individuales | Descartado con datos actuales |

---

## Referencias

- Global Fishing Watch API: https://globalfishingwatch.org/our-apis/
- Copernicus Marine: https://marine.copernicus.eu/
- Documentación Academy general: [ACADEMY.md](../../_archive/2026-06-academy/ACADEMY_VISION_2026-05.md)
- Backlog tareas: [academy_tareas_pendientes.md](../../TAREAS_PENDIENTES.md)
