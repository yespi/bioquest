# SEO — BioQuest y FotoFauna

## Arquitectura

| Entorno | URL BioQuest | Indexación |
|---------|--------------|------------|
| **PRE** | `https://bioquest.yespi.es/pre/` | `noindex` (evita duplicado) |
| **PRO** | `https://bioquest.yespi.es/` | `index,follow` |

El deploy `deploy-to-pro.sh` reescribe URLs `/pre/` → `/` y regenera `sitemap.xml` + `robots.txt` PRO. También pone `index,follow` en `index.html` PRO (PRE sigue con `noindex`).

## Problema conocido (jun 2026)

Si PRO muestra `noindex` en el HTML o `robots.txt` de PRE en la raíz, el deploy no había parcheado `index.html` tras el rsync. Los scripts `seo-*-build.mjs` lo corrigen en cada deploy PRO.

## Scripts

```bash
# PRE (desarrollo)
node /mnt/scripts/fauna/seo-bioquest-build.mjs --base https://bioquest.yespi.es/pre --out ./docker/bioquest/webapp/public-pre
node /mnt/scripts/fauna/seo-fotofauna-build.mjs --out ./docker/ecosistema-fauna/webapp/public-pre

# PRO (automático en deploy)
node /mnt/scripts/fauna/seo-bioquest-build.mjs --base https://bioquest.yespi.es --out ./docker/bioquest/webapp/public
```

## Landings indexables (`/seo/`)

- `ciencia-ciudadana.html` — guía + enlaces plataformas
- `cc-marina.html`, `cc-terrestre.html` — cursos (JSON-LD Course)
- `atlas.html`, `faunadex.html` — herramientas (JSON-LD WebApplication)
- `index.html` — hub Academy

## iNaturalist y monetización

Página legal: `/legal/terceros-y-licencias.html` (BioQuest + FotoFauna).

- No somos producto oficial iNaturalist.
- FotoFauna publica en la **cuenta del usuario**, no revende datos.
- Collages Academy: uso educativo no comercial con atribución.
- Líneas rojas de monetización documentadas en legal.

## Search Console (pendiente manual)

1. Añadir propiedad `https://bioquest.yespi.es`
2. Añadir propiedad `https://fotofauna.yespi.es`
3. Enviar sitemaps:
   - `https://bioquest.yespi.es/sitemap.xml`
   - `https://fotofauna.yespi.es/sitemap.xml`

## Deploy

```bash
bash ./docker/bioquest/deploy-to-pro.sh
bash ./docker/ecosistema-fauna/deploy-to-pro.sh
```
