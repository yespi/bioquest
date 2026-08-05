# Biología marina básica — Syllabus y preguntas de evaluación

Curso Academy `cc-marina` · PRE BioQuest · build `bq046edux1`

## Objetivo

Al finalizar el curso el estudiante debe poder **ver todas las diapositivas**, entender los conceptos y **responder por sí mismo** las preguntas de evaluación listadas al final.

Formato de estudio: **índice lateral por módulos** + **diapositiva a pantalla completa** con diagrama SVG y texto detallado.

---

## Módulos (7 días × 15 diapositivas)

| Día | Título | Contenido principal |
|-----|--------|---------------------|
| 1 | Qué es la ciencia ciudadana | Definición, proyectos, comunidad/hábitat/entorno |
| 2 | Colaborar con los científicos | Sin formación formal, plataformas, calidad del dato |
| 3 | Zonas del mar y exploración | Snorkel, buceo, ROV, submarinos, dragas, plancton |
| 4 | Pisos litorales | Supralitoral → circalitoral, comunidades costeras |
| 5 | Bentos y entorno | Bentos, algas, plantas; **lo que rodea** a cada especie |
| 6 | Fauna por profundidad | Familias, alimentación, reproducción, modo de vida |
| 7 | Muestreo y evaluación | Técnicas CC/profesionales, Atlas, repaso final |

Script de reordenación: `edu-restructure-cc-marina.mjs`

Fuente de verdad del contenido desplegado: `public-pre/data/edu-seed.json` → `courses.cc-marina`.

---

## Preguntas que debe poder responder el estudiante

### Factores y comunidades

1. **¿Qué influye en el tipo de especies de cada zona del mar?**  
   Cantidad y calidad de luz, corrientes, disponibilidad de nutrientes, presión, tipo de fondos, competencia, depredación.

2. **Organismos que sintetizan luz:** fotosintéticos (algas y plantas). Capturan la luz y generan vida.

3. A más profundidad: organismos más grandes y menos dependientes de la luz; cambios menos bruscos.

4. **Comunidad:** grupo de especies que viven juntas (gorgonias, esponjas, poliquetos, algas calcáreas).

5. **Hábitat:** lugar con condiciones adecuadas para la vida de una especie o comunidad.

### Pisos litorales

| Piso | Profundidad (orientativa) | Características |
|------|---------------------------|-----------------|
| Supralitoral | 0–5 m | Casi nunca sumergida; desecación; pobres en especies |
| Mesolitoral | 5–10 m | Mareas y oleaje; más diversidad |
| Infralitoral | 10–15 m (+) | Siempre cubierta; algas; competencia |
| Circalitoral | +20 m | Algás esciáfilas; poca luz |

### Bentos

- Conjunto de especies del fondo acuático (bentónico).
- Muchos se alimentan del plancton y lo regeneran.
- **Suspensívoros:** comen lo que da la corriente.
- **Sésiles** (quietos) o **móviles**.

### Vegetales vs animales

| | Vegetales | Animales |
|---|-----------|----------|
| Energía | Fotosíntesis → materia orgánica | Se alimentan |
| Algas | Sin tejidos diferenciados, sin flores | — |
| Plantas | Raíz, tallo, hojas, flores (fanerógamas) | — |

Tipos de algas: verdes, pardas, rojas, rojas calcáreas.

### Taxonomía animal (resumen)

- **Esponjas:** clonación, hermafroditas.
- **Cnidarios:** urticantes; corales, gorgonias, medusas, anémonas.
- **Poliquetos:** segmentados, brazos.
- **Briozoos:** colonias, tentáculos no urticantes.
- **Crustáceos:** exoesqueleto (cangrejo).
- **Equinodermos:** espinas, regeneración (estrella de mar).
- **Ascidias:** sifones, hermafroditas.
- **Opistobranquios:** ceratas, rinóforos, Flabellina.
- **Moluscos:** gasterópodos, bivalvos (mejillón, nacra), cefalópodos (pulpo, sepia).

### Peces

Criterios: forma, color (variable), grupo/solitario, movimiento, proximidad al fondo, aletas, boca.

| Familia | Rasgo | Ejemplos |
|---------|-------|----------|
| Lábridos | Labios, colores | Doncella, Vieja |
| Serránidos | Grandes depredadores | Mero, barracuda |
| Blénidos | Comprimidos, sin escamas | Bavosa de plomall |
| Góbidos | Deprimidos, escamas | Gobius bucchichi |

Otros: peces de pradera (salpa), fondo blando (raya), agua abierta (serviola, pez luna).

### Plancton y necton

- **Plancton:** deriva con corriente.
- **Fitoplancton:** luz, CO₂ → O₂.
- **Zooplancton:** migración diaria (día profundo, noche superficie).
- **Necton:** nada activamente.
- **Krill:** crustáceos; base alimentaria de ballenas.
- **Medusas:** cnidarios; primeros auxilios picadura (agua de mar, no dulce).

### Nacras y Posidonia

- **Pinna nobilis:** hasta 120 cm, praderas, peligro crítico; mortandad 2016 (protozoo).
- **Posidonia oceanica:** alguer, fanerógama, 0–40 m; florece oct.; beneficios CO₂, refugio, estabilización.

### Geología marina

- Tierra ~4.500 Ma; Pangea; placas divergentes.
- Plataforma continental <200 m (geobuceo habitual).
- Talud, cuenca oceánica, dorsales.
- Ambientes: litoral, nerítico, batial, abisal.

### Ciencia ciudadana

- Ventajas para biólogos, buceadores y clubs.
- Técnicas: transecto, censo gorgonia, peces indicadores, cuadrícula 20×20, observación estática, buceo + foto.
- Proyectos: GRAM, Posidonia, DIVA, OPK, Observadores del Mar, Natusfera.

---

## Diagramas SVG disponibles (`EduSlideVisual`)

| type | Uso |
|------|-----|
| `marine_factors` | Factores que influyen en especies |
| `littoral_zones` | Pisos litorales |
| `ocean_zones` | Zonas pelágicas |
| `bentos_pelagic` | Bentos vs pelágico |
| `plants_animals` | Vegetales vs animales |
| `taxon_cards` | Tarjetas taxonómicas (items en JSON) |
| `fish_families` | Familias de peces |
| `plankton` | Plancton y migración |
| `posidonia` | Pradera de Posidonia |
| `cc_examples` | Proyectos CC |
| `cc_techniques` | Técnicas de muestreo |
| `relief_submarine` | Relieve submarino |

---

## Pendiente de redacción (fases futuras)

- Ampliar ilustraciones por taxón (cnidarios, moluscos) con dibujos anatómicos.
- Quiz interactivo en BD alineado con preguntas de evaluación.
- Más días o sub-diapositivas si un tema requiere más de una pantalla.
- Sincronizar título en certificado API con «Biología marina básica».

---

## Referencias técnicas

- UI: `AcademyEduView.js`, `css/bq-edu-slides.css`
- Visuales: `views/academy-edu/EduSlideVisual.js`
- API: `routes_academy_edu.py` → `/ff-api/academy/edu/courses/{id}/day/{n}`
