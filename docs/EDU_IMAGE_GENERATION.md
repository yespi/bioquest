# Generación automática de láminas Academy

Pipeline para ilustrar diapositivas de cursos BioQuest Academy con **Stable Diffusion local** (Automatic1111 / SD WebUI) en **HanSolo**.

## Política (jun 2026)

**Solo A1111 local.** No se usan Pollinations, OpenRouter, Hugging Face ni otros backends cloud para láminas educativas.

| Backend | Estado |
|---------|--------|
| **A1111 (SD WebUI)** | ✅ **Único activo** — HanSolo GTX 1050 Ti, modelo DreamShaper 8 |
| ~~OpenRouter FLUX~~ | ❌ texto alucinado |
| ~~Pollinations~~ | ❌ deshabilitado |
| ~~Hugging Face~~ | ❌ deshabilitado |

A1111 = [AUTOMATIC1111/stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui) — interfaz web sobre **Stable Diffusion 1.5** (checkpoint DreamShaper 8). Calidad alta, generación lenta (~1–3 min/lámina en 4 GB VRAM).

## Infraestructura HanSolo

| Parámetro | Valor |
|-----------|-------|
| API | `http://localhost:7860` (también `localhost:7860`) |
| Servicio | `sd-webui.service` |
| Modelo | `dreamshaper_8.safetensors` |
| VRAM | GTX 1050 Ti 4 GB → `EDU_A1111_LOW_VRAM=1`, 1024×576, 22 steps |

Config operativa: `/mnt/scripts/fauna/edu-illustrations/.env.gpu`

## Comandos

```bash
cd /mnt/scripts/fauna/edu-illustrations
set -a && source .env.gpu && set +a

# Verificar A1111
node edu-probe-backends.mjs

# Manifiesto (todas las láminas del curso)
node edu-build-manifest.mjs cc-marina --all-slides --force

# Generar con A1111 (reemplaza láminas cloud/placeholder)
node edu-generate-batch.mjs cc-marina --replace-placeholders --retry-rejected --delay=8000

# Regenerar todo el curso (p. ej. tras cambio de prompts)
node edu-generate-batch.mjs cc-marina --force-regenerate --delay=8000

# Batch continuo hasta completar (publica PRE cada 10 rondas y al final)
nohup bash run-a1111-until-done.sh cc-marina cc-terrestre >> logs/a1111-until-done.log 2>&1 &

# Publicar PRE manualmente (sync JSON + bump + cache-bust láminas)
bash edu-a1111-publish-pre.sh cc-marina cc-terrestre

# Aplicar rutas al seed
node edu-apply-to-seed.mjs cc-marina cc-terrestre --sync-backend
```

## Variables de entorno

| Variable | Default | Descripción |
|----------|---------|-------------|
| `EDU_IMAGE_BACKEND` | `auto` → `a1111` | Backend único |
| `EDU_IMAGE_BACKENDS` | `a1111` | Sin fallback cloud |
| `SD_WEBUI_URL` | `http://localhost:7860` | API A1111 en HanSolo |
| `EDU_A1111_LOW_VRAM` | `1` | Resolución/steps reducidos (4 GB VRAM) |
| `EDU_A1111_WIDTH` / `HEIGHT` | `1024` / `576` | Resolución láminas |
| `EDU_A1111_STEPS` | `22` | Pasos de difusión |
| `EDU_ILLUSTRATION_DELAY_MS` | `8000` | Pausa entre imágenes |
| `EDU_ILLUSTRATION_MIN_BYTES` | `25000` | Umbral «ya generada» |

## Pipeline nocturno

`bq-edu-illustrations-nightly.sh` y `run-a1111-until-done.sh`:

1. Collages iNaturalist, posters, placeholders (sin GPU).
2. Láminas SD vía A1111 si WebUI responde.
3. **Sin fallback cloud** — si A1111 no está disponible, se omiten láminas SD.

## Archivos

| Ruta | Rol |
|------|-----|
| `edu-generate-batch.mjs` | Lote + manifiesto |
| `edu-probe-backends.mjs` | Diagnóstico |
| `lib/backends/a1111.mjs` | SD WebUI local |
| `run-a1111-until-done.sh` | Batch continuo hasta completar |
| `.env.gpu` | URL HanSolo + parámetros VRAM |
| `manifests/*.json` | Estado por curso |

## Rechazar láminas cloud

Para marcar láminas generadas con backend cloud y regenerar con A1111:

```bash
node edu-reject-backend-batch.mjs pollinations cc-marina --reason "solo A1111 local"
node edu-reject-backend-batch.mjs openrouter cc-terrestre cc-marina --reason "solo A1111 local"
node edu-generate-batch.mjs cc-marina cc-terrestre --retry-rejected --replace-placeholders --delay=8000
```

## Placeholders vs láminas IA

Los PNG de `edu-generate-placeholders.py` (~17 KB) evitan 404 en PRE. Sustituir con:

```bash
node edu-generate-batch.mjs cc-terrestre --replace-placeholders --retry-rejected
```
