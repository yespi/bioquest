# Fotografía de animales — curso Academy `foto-fauna`

> PRE BioQuest · 7 días × 15 diapositivas · build `bq054foto1`

## Objetivo

Formar al observador para **fotografía de fauna con valor científico** (ciencia ciudadana): exposición correcta en campo, macro y tele en tierra, aves en movimiento, y **submarina con Olympus TG-7** (modo Pez: Pelágico y Microscopio).

Manual completo: [`academy-manuals/GUION_foto-fauna.md`](academy-manuals/GUION_foto-fauna.md)

## Capítulos

| Día | Tema | Contenido clave |
|-----|------|-----------------|
| 1 | Triángulo de exposición | ISO auto, apertura, obturación, contraste, modos A/S/M, EV |
| 2 | Macro terrestre | 1:1, f/8–16, flash difuso, ética, planta huésped |
| 3 | Tele 300 mm | AF-C, 1/500 s, lastre/monopie, bienestar animal |
| 4 | Aves | 1/2000 vuelo, plumaje claro/oscuro, AF tracking, burst |
| 5 | Olympus TG-7 | Modo Pez, **Pelágico** vs **Microscopio**, flash, checklist |
| 6 | Luz y flotabilidad | Backscatter, ángulo flash, lastre, snorkel vs buceo |
| 7 | Buscar y registrar | Nudibranquios por alimento, pulpos por cáscaras, iNat/Atlas |

## Regenerar contenido

```bash
node /mnt/scripts/fauna/edu-illustrations/edu-scaffold-foto-fauna.mjs --sync-backend
node /mnt/scripts/fauna/edu-illustrations/edu-build-manual-scripts.mjs foto-fauna --sync-seed --sync-backend
```

## Notas TG-7

Los nombres exactos en menú pueden variar según firmware; en el curso se usan **Pelágico** (agua abierta) y **Microscopio** (macro subacuana). Consultar manual Olympus para la versión instalada.
