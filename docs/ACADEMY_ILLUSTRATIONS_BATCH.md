# Generación nocturna de ilustraciones — Academy

> Scripts en `/mnt/scripts/fauna/edu-illustrations/`  
> Cursos piloto: **Biología marina básica** (`cc-marina`) y **Ciencia ciudadana terrestre** (`cc-terrestre`).

## Objetivo

Generar **una lámina PNG por diapositiva** (donde aplique), con **prompts en castellano**, en local y sin límites de API cloud. Pensado para ejecutarse **de noche** mientras el PC/servidor tiene Stable Diffusion activo.

## Requisitos

1. **Stable Diffusion WebUI (Automatic1111)** con API activa:
   ```bash
   # En webui-user.sh o equivalente:
   export COMMANDLINE_ARGS="--api --listen"
   ```
2. URL HanSolo: `http://localhost:7860` (variable `SD_WEBUI_URL`; ver `.env.gpu`).
3. Modelo activo: **DreamShaper 8** (`dreamshaper_8.safetensors`) — SD 1.5 naturalista.
4. Node.js 18+ (ya disponible en HanSolo).

### Alternativa ComfyUI

Si usas ComfyUI en lugar de A1111, puedes exponer la API compatible o añadir un backend `lib/backends/comfyui.mjs` (pendiente). Por ahora el script usa **solo A1111**.

## Estructura

| Script | Función |
|--------|---------|
| `edu-build-manifest.mjs` | Lee `edu-seed.json` y crea manifiesto de diapositivas pendientes |
| `edu-generate-batch.mjs` | Genera PNG en lote vía SD WebUI |
| `edu-apply-to-seed.mjs` | Escribe `visual.src` en cada diapositiva del seed |
| `edu-build-cc-collages.mjs` | Descarga fotos CC iNaturalist → `edu-cc-photos.json` |
| `edu-apply-collages-to-seed.mjs` | Asigna `photo_collage` a diapositivas taxonómicas |
| `bq-edu-illustrations-nightly.sh` | Orquesta collages CC + láminas SD (cron 03:00) |

Salida de imágenes:

```
public-pre/assets/edu/cc-marina/d1-s01.png
public-pre/assets/edu/cc-terrestre/d3-s07.png
```

Manifiestos: `/mnt/scripts/fauna/edu-illustrations/manifests/{curso}.json`  
Logs: `/mnt/scripts/fauna/edu-illustrations/logs/`

## Uso manual

```bash
cd /mnt/scripts/fauna/edu-illustrations

# 1. Manifiesto (cuántas faltan)
node edu-build-manifest.mjs cc-marina cc-terrestre

# 2. Prueba sin generar (solo muestra prompts en castellano)
node edu-generate-batch.mjs cc-marina --limit=3 --dry-run

# 3. Generar (SD WebUI debe estar corriendo)
node edu-generate-batch.mjs cc-marina --limit=10 --delay=3000

# 4. Aplicar al seed y sincronizar backend
node edu-apply-to-seed.mjs cc-marina cc-terrestre --sync-backend
```

## Cron nocturno

```bash
# Editar crontab -e (o ya instalado en HanSolo):
0 3 * * * /mnt/scripts/fauna/edu-illustrations/bq-edu-illustrations-nightly.sh >> /mnt/scripts/fauna/edu-illustrations/logs/cron.log 2>&1
```

Orden nocturno: (0) guiones manual SSI/PADI, (1) collages iNaturalist, (2) láminas SD si WebUI responde en `:7860`.

Ver también [`academy-manuals/README.md`](academy-manuals/README.md).

Variables opcionales:

```bash
export SD_WEBUI_URL=http://localhost:7860
export EDU_IMAGE_BACKEND=a1111
export EDU_IMAGE_BACKENDS=a1111
export EDU_ILLUSTRATION_COURSES="cc-marina cc-terrestre"
export EDU_ILLUSTRATION_DELAY_MS=2500   # pausa entre imágenes (ms)
```

El script **reanuda**: las entradas con PNG ya generado quedan en `status: done` y se saltan.

## Cobertura objetivo

| Curso | Días | Diapos./día | Total | Con lámina (objetivo) |
|-------|------|-------------|-------|------------------------|
| cc-marina | 7 | 15 | 105 | ≥60 % (~63+) |
| cc-terrestre | 7 | 15 | 105 | ≥60 % (~63+) |

Tras la primera pasada nocturna, ejecuta de nuevo `edu-build-manifest.mjs` para ver cuántas quedan pendientes.

## Prompts en castellano

Los prompts se construyen en `lib/prompts-es.mjs` a partir del **título del día**, **título de diapositiva** y **extracto del texto**. Estilo fijo: lámina científica, acuarela/tinta, fondo claro, sin texto en imagen.

## Esqueleto CC terrestre

```bash
node /mnt/scripts/fauna/edu-scaffold-cc-terrestre.mjs
```

Regenera 7×15 diapositivas en castellano si se borra el curso.

## Reinicio del backend

Tras `--sync-backend`, reinicia el contenedor API si cachea el seed en memoria (normalmente lee disco en cada petición).

## Relacionado

- [`ACADEMY_SLIDES.md`](ACADEMY_SLIDES.md) — formato diapositiva  
- [`CURRICULUM_MARINA_BASICA.md`](CURRICULUM_MARINA_BASICA.md) — temario marina
