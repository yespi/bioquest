# SEO, analítica y monetización — BioQuest / FotoFauna

> Actualizado: jun 2026 · Entorno canónico: PRE BioQuest `/pre/`, FotoFauna `fotofauna.yespi.es`

## 1. Objetivo

Atraer tráfico orgánico (SEO) y medir uso (GA4) **sin** incumplir términos de iNaturalist, Minka u otras APIs, ni degradar la experiencia educativa.

---

## 2. Ficheros SEO desplegados (PRE)

| Fichero | URL | Propósito |
|---------|-----|-----------|
| `robots.txt` | `https://bioquest.yespi.es/pre/robots.txt` | Permite crawl de `/pre/seo/` y legal; bloquea `/ff-api/` |
| `sitemap.xml` | `https://bioquest.yespi.es/pre/sitemap.xml` | URLs indexables para Google/Bing |
| `seo/*.html` | `/pre/seo/` | Landings estáticas (cursos Academy) con JSON-LD `Course` |
| Meta OG + canonical | `index.html` | Snippet en redes y deduplicación |

**FotoFauna (public-pre):** `robots.txt`, `sitemap.xml`, meta mejorados en `index.html`.

### Limitación SPA

La app principal (`#atlas`, `#academy`) es una SPA con rutas hash. Google indexa peor el contenido solo en hash. **Estrategia actual:** landings estáticas en `/pre/seo/` enlazadas desde sitemap; la app sigue en hash hasta una fase 2 (URLs limpias o prerender).

Regenerar sitemap tras nuevos cursos:

```bash
python3 /mnt/scripts/bioquest/generate-bioquest-sitemap.py
```

---

## 3. Google Analytics 4

### Estado

- Consent Mode v2: analítica **denegada** por defecto; publicidad **siempre denegada** en el banner actual.
- `bq-analytics.js` / `ff-analytics.js`: cargan GA4 solo tras aceptar cookies analíticas.
- Measurement ID en `config/analytics.json` (no commitear IDs reales en git si se prefiere; generar desde `.api-keys`).

### Referencia (cuenta Yespi · jul 2026)

| App | Measurement ID | Property ID |
|-----|----------------|-------------|
| BioQuest | `G-EK693HPKN8` | `541021182` |
| FotoFauna | `G-EG7QWMVP2K` | `540997387` |

Cuenta Analytics: `397445154` · Canónico: `(configure via environment)`

### Activación (hecho)

1. ~~Crear propiedades GA4~~ ✓
2. IDs en `(configure via environment)` ✓

3. Sincronizar config y recargar nginx:

   ```bash
   bash /mnt/scripts/bioquest/sync-analytics-config.sh
   docker exec bioquest-webapp nginx -s reload
   # FotoFauna: copiar public-pre → public si aplica deploy PRO
   ```

4. Verificar en GA4 → Informes en tiempo real (aceptar cookies en navegador limpio).

5. **Esperar 4–6 semanas** de datos antes de decidir monetización (criterio acordado).

---

## 4. Publicidad no invasiva — análisis de compatibilidad

### Principios Yespi

1. **Educación primero** — Academy y fichas no deben competir visualmente con anuncios.
2. **Consentimiento explícito** — Cualquier red programática (AdSense, etc.) requiere banner ampliado (`ad_storage`) distinto del actual solo-analítica.
3. **Atribución de terceros** — Fotos iNat/Minka llevan crédito; no envolver creatividades comerciales alrededor de contenido CC-BY de observadores.

### iNaturalist ([Terms of Use](https://www.inaturalist.org/pages/terms))

| Restricción | Implicación para ads |
|-------------|---------------------|
| Prohibido contenido comercial no deseado para manipular SEO o redirigir tráfico | No usar datos iNat como “cebo” en landings spam; landings SEO deben ser editoriales reales (OK con `/pre/seo/`) |
| API para apps, no scraping masivo | OK uso actual vía `photo-proxy` con throttling |
| Prohibido entrenar modelos comerciales con datos iNat | No aplica a ads, pero refuerza no monetizar directamente el dataset |
| Atribución y licencias de observadores | Mantener créditos visibles; **no colocar banners sobre carruseles** de fotos iNat |

**Conclusión iNat:** Publicidad **permitida con matices** en páginas propias (hubs legales, listados de cursos estáticos, sidebar en vistas sin fotos de terceros). **Evitar:** AdSense inyectado sobre grids de observaciones, lightboxes con fotos iNat, o páginas cuyo contenido principal sea media iNat sin valor editorial propio.

### Minka-SDG / API propia

Revisar ToS del proveedor antes de ads adyacentes a datos Minka. Mientras no haya cláusula explícita: tratar como iNat (atribución + no monetizar el dato en bruto).

### OMDb (películas Academy)

Uso bajo API key personal; no redistribuir posters como producto. Ads en hub “Películas” solo si no sustituyen metadatos OMDb ni violan [OMDb terms](http://www.omdbapi.com/) (uso no comercial de assets con atribución).

### Google AdSense (si se adopta)

| Zona | Riesgo | Recomendación |
|------|--------|---------------|
| `/pre/seo/*` landings | Bajo | Candidato: 1 bloque footer tras contenido editorial |
| Legal / cookies | Bajo | Evitar (mala UX en compliance) |
| Academy slides | **Alto** | **No** — interrumpe formación |
| Atlas mapa / FaunaDex carrusel | **Alto** | **No** — contenido iNat/Minka |
| FotoFauna editor de fotos | **Alto** | **No** |

**Alternativas preferibles a AdSense programático:**

- **Patrocinios editoriales** (ONG marina, tiendas de buceo ético) en hub Orgs/Voluntariado — ya alineado con contenido.
- **Afiliación contextual** (libros de campo, guías) solo en artículos estáticos, con `rel="sponsored"`.
- **Donaciones / Ko-fi** en footer — sin cookies de publicidad.

### Implementación técnica futura (fase monetización)

1. Ampliar `bq-consent.js` con tercera opción o sub-banner “Personalización de anuncios” (Consent Mode `ad_storage`).
2. Componente `BqAdSlot` — solo monta si `ad_storage === granted` y `data-ad-zone` está en allowlist (`seo-footer`, `orgs-hub`).
3. Nunca cargar scripts DoubleClick en rutas `#academy` con slides activos ni en `#explore` con galería iNat.

---

## 5. Checklist operativo

- [x] IDs GA4 en `.api-keys` (measurement + property) + `sync-analytics-config.sh`
- [ ] Service account Google (`GOOGLE_SA_JSON`) para informe semanal automático
- [ ] Enviar sitemap en [Google Search Console](https://search.google.com/search-console) (propiedades bioquest + fotofauna)
- [ ] Comprobar `curl -s https://bioquest.yespi.es/pre/robots.txt`
- [ ] Tras 4–6 semanas GA4: revisar páginas de entrada y decidir ads vs patrocinios
- [ ] Deploy PRO BioQuest/FotoFauna cuando Gustavo autorice (copiar `public-pre` → `public`)

---

## 6. Referencias

- `./docker/bioquest/webapp/public-pre/composables/bq-analytics.js`
- `./docker/bioquest/webapp/public-pre/composables/bq-consent.js`
- `/mnt/scripts/bioquest/sync-analytics-config.sh`
- `/mnt/scripts/bioquest/generate-bioquest-sitemap.py`
- [iNaturalist API Recommended Practices](https://www.inaturalist.org/pages/api+recommended+practices)
