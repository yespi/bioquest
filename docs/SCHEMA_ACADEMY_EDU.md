# Academy educativa — esquema SQL (borrador)

> **Estado:** tablas de progreso activas en PRE (jun 2026). Catálogo sigue en `edu-seed.json`.

## Tablas propuestas

```sql
-- Catálogo de cursos
CREATE TABLE academy_edu_courses (
    id              TEXT PRIMARY KEY,          -- cc-marina, aves, …
    title           TEXT NOT NULL,
    days            SMALLINT NOT NULL,
    icon            TEXT,
    sort_order      SMALLINT DEFAULT 0,
    active          BOOLEAN DEFAULT TRUE,
    created_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Días / lecciones (contenido IA en academy_ai_text kind=course_day)
CREATE TABLE academy_edu_course_days (
    course_id       TEXT REFERENCES academy_edu_courses(id),
    day_num         SMALLINT NOT NULL,
    title           TEXT,
    PRIMARY KEY (course_id, day_num)
);

-- Progreso por usuario
CREATE TABLE academy_edu_progress (
    user_id         INTEGER REFERENCES users(id),
    course_id       TEXT REFERENCES academy_edu_courses(id),
    day_completed   SMALLINT DEFAULT 0,
    quiz_scores     JSONB DEFAULT '{}',
    updated_at      TIMESTAMPTZ DEFAULT NOW(),
    PRIMARY KEY (user_id, course_id)
);

-- Media curada (alternativa a edu-seed.json)
CREATE TABLE academy_edu_media (
    id              SERIAL PRIMARY KEY,
    title           TEXT NOT NULL,
    source          TEXT,
    url             TEXT NOT NULL,
    topics          TEXT[],
    related_inat_ids INTEGER[],
    lang            TEXT DEFAULT 'es'
);

-- Organizaciones
CREATE TABLE academy_edu_orgs (
    id              SERIAL PRIMARY KEY,
    name            TEXT NOT NULL,
    url             TEXT NOT NULL,
    blurb           TEXT,
    tags            TEXT[]
);
```

## API (PRE `bq044edu1`)

| Método | Ruta | Auth | Descripción |
|--------|------|------|-------------|
| GET | `/academy/edu/courses` | — | Lista cursos preview |
| GET | `/academy/edu/courses/{id}/day/{n}` | — | Lección día n |
| GET | `/academy/edu/seed` | — | JSON completo |
| GET | `/academy/edu/progress` | Bearer | Progreso de todos los cursos |
| GET | `/academy/edu/progress/{id}` | Bearer | Progreso de un curso |
| POST | `/academy/edu/progress/{id}/complete-day` | Bearer | `{ "day": N }` — marca día |
| GET | `/academy/edu/progress/{id}/certificate` | Bearer | Certificado si curso completo |

Invitados: progreso en `localStorage` (`bq_edu_progress_v1`).

## API histórica (stub)

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/academy/edu/courses` | Lista 5 cursos preview |
| GET | `/academy/edu/seed` | JSON `edu-seed.json` (media + orgs) |

## Seeds estáticos

- Frontend: `/pre/data/edu-seed.json`
- Backend: `ecosistema-fauna/backend/data/edu-seed.json`

## Fases

| Fase | Entregable |
|------|------------|
| 0 | Docs + seeds JSON ✅ |
| 1 | Landing `#academy` (AcademyEduView) ✅ swap PRE |
| 2 | 1 curso piloto + progreso en BD ✅ (`academy_edu_progress`) |
| 3 | 5 cursos + export offline |
