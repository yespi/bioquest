# CC Marina — Curso robusto v2

> **Título:** Biología marina básica · Ciencia ciudadana en el Mediterráneo  
> **Formato:** 12 días × 10 diapositivas = **120 láminas**  
> **Canónico:** `./docker/bioquest/webapp/public-pre/data/courses/cc-marina.json`

---

## 1. Benchmark mundial (qué aportan otros cursos)

### España — Observadores del Mar (CSIC)

- **Modelo:** plataforma + protocolos por proyecto, no un curso lineal único.
- **15 proyectos** activos (desiertos submarinos, redes fantasma, especies exóticas, aves marinas, praderas…).
- **Flujo:** registro → elegir proyecto → guías de identificación → subir obs con **ubicación y fecha** → validación científica.
- **Aporta a nuestro curso:** énfasis en **calidad del dato**, proyectos temáticos, mortalidades/gorgonias, invasoras, vínculo playa–snorkel–buceo.
- Referencia: [Observadores del Mar](https://www.observadoresdelmar.es) · [EU-Citizen.Science](https://eu-citizen.science/project/5)

### PADI AWARE — Dive Against Debris

- **Modelo:** especialidad de buceo con toolkit estandarizado (Survey Guide, Data Card, ID Guide).
- **5 pasos:** pesar → clasificar → registrar → disponer → reportar en portal global.
- **Aporta:** estructura **protocolo + tarjeta de datos + mapa de impacto**; podemos reflejarlo en la sección de muestreo y en capturas del Atlas.
- Referencia: [PADI AWARE Teaching Hub](https://app.padiaware.org/publication/padi-aware-online-teaching-hub)

### Reef Check / SeagrassWatch / OPK

- **Reef Check:** transectos con fichas de substrato + peces + invertebrados (indicadores de salud).
- **SeagrassWatch / GRAM Posidonia:** monitoreo de fanerógamas, densidad y límites inferiores.
- **OPK (opistobranquios):** identificación fotográfica de nudibranquios — encaja con collages de thumbs.
- **Aporta:** un día dedicado a **transectos** y otro a **grupos taxonómicos** con fotos reales.

### iNaturalist / Zooniverse (global)

- **Modelo:** foto + sugerencia IA + validación comunitaria + licencias CC.
- **Aporta:** flujo Minka + complemento iNat; ética de identificación («mejor no sé que mal ID»).

### Coursera / edX (biología marina genérica)

- Suele cubrir: zonación litoral, adaptaciones, cambio climático, acidificación, redes tróficas.
- **No cubren** nuestra plataforma ni Minka-SDG — **diferenciador BioQuest**.

---

## 2. Diferenciadores BioQuest (obligatorios en v2)

Cada alumno debe ver **cómo usar nuestro ecosistema**:

| Utilidad | Dónde en el curso | Visual |
|----------|-------------------|--------|
| **Atlas** — mapa de observaciones, heatmap, capas | Día 8 | `platform-atlas-map.png` |
| **Atlas** — gráficas YoY, esfuerzo, IUCN | Día 8 | `platform-atlas-charts.png` |
| **FaunaDex** — zonas, especies vistas, medallas | Día 9 | `platform-faunadex.png` |
| **FotoFauna** — subida, Auto-ID, Minka | Día 9 | `platform-fotofauna.png` |
| **Minka-SDG** — flujo publicar obs | Día 2 y 8 | `platform-minka-flow.png` |
| **Thumbs reales** — collages por grupo taxonómico | Días 10–11 | `edu-thumb-collages.json` |

---

## 3. Estructura 12 × 10 (120 diapositivas)

| Día | Título | Contenido clave |
|-----|--------|-----------------|
| 1 | Qué es la CC marina | Concepto, comunidad/hábitat/entorno, factores, proyectos ES |
| 2 | Colaborar sin ser científico | Protocolo, calidad dato, Minka, iNat, OdM |
| 3 | Zonas del mar y exploración | Pelágico, Cousteau, profundidad, exploración histórica |
| 4 | Comunidades litorales | Pisos infralitoral/mediolitoral, roca/arena/pradera |
| 5 | Bentos y entorno | Substrato, presión, entorno de la especie |
| 6 | Fauna por profundidad | Familias, modo de vida, indicadores |
| 7 | Muestreo, ética y evaluación | Transectos, ética, certificado |
| 8 | **BioQuest Atlas** | Mapas, capas, gráficas, conservación, buques |
| 9 | **FotoFauna y FaunaDex** | Subir fotos, medallas, mis obs, sync Minka |
| 10 | **Galería taxonómica I** | Nudibranquios, elasmobranquios (collages thumbs) |
| 11 | **Galería taxonómica II** | Cnidarios, algas, peces costeros, cefalópodos |
| 12 | Proyectos, amenazas y cierre | Invasoras, cambio climático, plan de acción ampliado |

---

## 4. Pipeline de imágenes

```bash
# Collages desde thumbs precache (Minka/iNat)
python3 /mnt/scripts/fauna/edu-illustrations/edu-build-thumb-collages.py

# Láminas estáticas plataforma
python3 /mnt/scripts/fauna/edu-illustrations/edu-build-platform-visuals.py

# Ampliar curso a 120 diapos
node /mnt/scripts/fauna/edu-illustrations/edu-expand-cc-marina-v2.mjs

# Manifiesto SD (cuando WebUI activo)
node edu-build-manifest.mjs cc-marina
node edu-generate-batch.mjs cc-marina --limit=20
```

Assets: `public-pre/assets/edu/cc-marina/` · `public-pre/assets/edu/platform/`

---

## 4b. Láminas CC (desde 2026-06-19 — sin A1111)

Generación local A1111 **cancelada** (artefactos: anatomías imposibles, texto ilegible).

**Nuevo pipeline:**

1. `node edu-cc-gallery-fetch.mjs cc-marina` — candidatos Wikimedia Commons / FMIB (dominio público o CC-BY).
2. Revisión manual o semi-automática → descarga a `assets/edu/cc-marina/dNN-sSS.png`.
3. Si la lámina original está en **inglés**, la UI muestra **caption/toast en castellano** (`captionEs` del slide) superpuesto.

**Fuentes CC recomendadas:**

| Fuente | Licencia | Uso |
|--------|----------|-----|
| [Wikimedia Commons — FMIB](https://commons.wikimedia.org/wiki/Category:Images_from_the_Freshwater_and_Marine_Image_Bank) | Dominio público | Anatomías, larvas, esquemas clásicos |
| [Fish anatomy illustrations](https://commons.wikimedia.org/wiki/Category:Fish_anatomy_illustrations) | CC-BY / PD | Cortes, sistemas |
| [Marine biology diagrams](https://commons.wikimedia.org/wiki/Category:Marine_biology_diagrams) | Varias CC | Esquemas didácticos |
| NOAA / USGS (vía Commons) | PD | Cartas, organismos |

Scripts: `edu-purge-bad-illustrations.mjs` · `edu-stop-a1111.sh` · `edu-cc-gallery-fetch.mjs` · `edu-cc-gallery-apply.mjs` · `edu-archive-ai-slides.mjs`

---

## 5. Criterios de calidad v2

- **≥80 imágenes únicas** (PNG hasta 1920 px + collages thumbs + platform)
- Resolución láminas Commons: **máx. 1920 px** (Lanczos), fuente original Wikimedia (`edu-cc-gallery-apply.mjs --hq` para regenerar)
- Cada collage con **crédito** (thumb manifest o iNat)
- Texto **≥3 párrafos** por diapo conceptual; repasos con objetivos explícitos
- Enlaces `atlas_links` / `faunadex_links` donde aplique
- Sin reutilizar el mismo PNG en >1 diapo salvo repasos explícitos
