# BioQuest — `bioquest.yespi.es`

Plataforma de ciencia ciudadana: atlas de especies, cursos (Academy), medallas y mapa de buceo/excursiones.

| | |
|---|---|
| **Web** | https://bioquest.yespi.es/ (PRO) · `/pre/` (PRE) |
| **Código** | `./docker/bioquest/` — frontend en `webapp/public-pre/` |
| **Backend** | compartido con FotoFauna (`ecosistema-fauna`), misma BD PostgreSQL |
| **Tareas** | [`TAREAS_PENDIENTES.md`](../../TAREAS_PENDIENTES.md) · [`TAREAS_FINALIZADAS.md`](../../TAREAS_FINALIZADAS.md) |

**Ejes actuales** (`views/`): **Atlas** · **Academy** / AcademyEdu · **FaunaDex** + Medallas (Awards) · **Explore** / **Buceo**.

## Índice

### Arquitectura y plataforma
| Doc | Qué es |
|-----|--------|
| [`ARQUITECTURA.md`](ARQUITECTURA.md) | Arquitectura del sistema. ⚠️ Ver aviso: la sección de estructura de ficheros describe el legacy anterior a jun-2026 |
| [`NAMING_EJES.md`](NAMING_EJES.md) | Qué significa cada eje (Atlas / Academy / FaunaDex). Naming ya aplicado en código |
| [`FAUNA_CRONS.md`](FAUNA_CRONS.md) | Los 4 orquestadores de cron de FF + BQ |
| [`CHANGELOG.md`](CHANGELOG.md) | Changelog |
| [`PERFORMANCE_ACADEMY_FF.md`](PERFORMANCE_ACADEMY_FF.md) | Lentitud intermitente: causas y thumbnails WebP |

### Explore / Buceo
| Doc | Qué es |
|-----|--------|
| [`BUCEO_DISENO.md`](BUCEO_DISENO.md) | Diseño de la sección Buceo/Excursiones (en PRO desde 2026-07-04) |
| [`EXPLORE_CAPAS.md`](EXPLORE_CAPAS.md) | Capas de atmósfera: meteo, temperatura, viento, satélite |
| [`BUQUES_ECOLOGICOS.md`](BUQUES_ECOLOGICOS.md) | Catálogo de buques con función ecológica y capas de mapa |

### Academy (cursos)
| Doc | Qué es |
|-----|--------|
| [`ACADEMY_ROADMAP.md`](ACADEMY_ROADMAP.md) | Hoja de ruta de Academy |
| [`ACADEMY_SLIDES.md`](ACADEMY_SLIDES.md) | Criterio editorial: cada día = 1 diapositiva mínimo |
| [`SCHEMA_ACADEMY_EDU.md`](SCHEMA_ACADEMY_EDU.md) | Esquema SQL de progreso educativo |
| [`ACADEMY_AI_TEXTOS_BBDD.md`](ACADEMY_AI_TEXTOS_BBDD.md) | Textos generados por IA en `academy_ai_text` |
| [`ACADEMY_ILLUSTRATIONS_BATCH.md`](ACADEMY_ILLUSTRATIONS_BATCH.md) | Generación nocturna de ilustraciones |
| [`EDU_IMAGE_GENERATION.md`](EDU_IMAGE_GENERATION.md) | Pipeline de láminas con Stable Diffusion local |
| [`IMG_GEMINI.md`](IMG_GEMINI.md) | Láminas con Gemini. **F2 bloqueada** por decisión de billing |

### Cursos concretos
| Doc | Qué es |
|-----|--------|
| [`CC_MARINA_CURSO.md`](CC_MARINA_CURSO.md) | Biología marina básica — 12 días × 10 láminas |
| [`CURRICULUM_MARINA_BASICA.md`](CURRICULUM_MARINA_BASICA.md) | Syllabus y preguntas de evaluación de `cc-marina` |
| [`FOTO_FAUNA_CURSO.md`](FOTO_FAUNA_CURSO.md) | Curso `foto-fauna` — 7 días × 15 láminas |
| [`academy-manuals/`](academy-manuals/) | Guiones largos de los cursos (aves, cc-marina, cc-terrestre, foto-fauna, nudibranquios) |

### SEO
| Doc | Qué es |
|-----|--------|
| [`SEO.md`](SEO.md) | Arquitectura de indexación BQ + FF |
| [`SEO_MONETIZACION.md`](SEO_MONETIZACION.md) | SEO, analítica y monetización |

### Propuestas sin implementar
| Doc | Qué es |
|-----|--------|
| [`PROPUESTA_AREAS_PROTEGIDAS.md`](PROPUESTA_AREAS_PROTEGIDAS.md) | Mapas de áreas protegidas y parques naturales |
| [`PROPUESTA_MAPAS_CIENTIFICOS.md`](PROPUESTA_MAPAS_CIENTIFICOS.md) | Mapas y visualizaciones científicas |

## Notas

- `/mnt/docs/bioquest` es un **symlink** a esta carpeta: los crons de QA escriben ahí con rutas fijas. Sus resultados (`qa_*.json`, `lighthouse/`, …) están en `.gitignore`.
- Auditorías y planes de junio 2026 ya ejecutados (Atlas perf, MITECO, orgs, QA marathon, migración de naming, propuesta original de Academy): [`_archive/2026-06-bioquest/`](../../_archive/2026-06-bioquest/).
- Visión original de Academy e histórico de tareas: [`_archive/2026-06-academy/`](../../_archive/2026-06-academy/).
