# BioQuest — Academy Charts & Graphs

Each species in the Academy has a detail page with interactive charts generated from real observation data.

## Species Charts

### 1. 📅 Fenología (Phenology)
- **What it shows**: Monthly distribution of observations throughout the year
- **X-axis**: Months (Jan–Dec)
- **Y-axis**: Number of observations
- **Data source**: Minka + iNaturalist observations
- **Purpose**: Learn when each species is most likely to be observed
- **Interpretation**: Peak months = best time to find this species

### 2. 📍 Distribución Geográfica (Geographic Distribution)
- **What it shows**: Map with observation points
- **Markers**: Each observation plotted on Mediterranean map
- **Heatmap mode**: Density visualization for abundant species
- **Data source**: GBIF + Minka occurrence records
- **Purpose**: Understand species range and habitat preferences

### 3. 📊 Profundidad (Depth Distribution)
- **What it shows**: Histogram of observation depths
- **X-axis**: Depth in meters
- **Y-axis**: Number of observations
- **Data source**: Observations with recorded depth
- **Purpose**: Learn depth preferences (intertidal, infralittoral, circalittoral)
- **Bins**: 5m intervals

### 4. 🌡️ Temperatura del Agua (Water Temperature)
- **What it shows**: Temperature range where species is observed
- **X-axis**: Water temperature (°C)
- **Y-axis**: Number of observations
- **Purpose**: Understand thermal preferences
- **Insight**: Helps predict species presence based on SST

### 5. 📈 Tendencia Poblacional (Population Trend)
- **What it shows**: Observation frequency over years
- **X-axis**: Years
- **Y-axis**: Observations per year (normalized)
- **Purpose**: Detect population changes
- **IUCN integration**: Compare with official population trend assessments

### 6. 🗺️ Perfil Batimétrico (Bathymetric Profile)
- **What it shows**: Vertical distribution along a transect
- **Interactive**: Click on map to draw transect line
- **Y-axis**: Depth
- **X-axis**: Distance along transect
- **Purpose**: Visualize species distribution across depth gradients

### 7. 🏷️ Especies Similares (Similar Species)
- **What it shows**: Confusable species comparison
- **Data source**: Cryptic pairs dataset (815 pairs)
- **Display**: Side-by-side photos with diagnostic differences
- **Purpose**: Learn to distinguish visually similar species

### 8. ⏰ Hora del Día (Time of Day)
- **What it shows**: Observation distribution by hour
- **X-axis**: Hours (0–23)
- **Y-axis**: Number of observations
- **Purpose**: Diurnal/nocturnal activity patterns
- **Insight**: Some species only observed at specific times

### Chart Help System
- Each chart has an explanatory tooltip (`chartHelp`)
- Describes what the chart shows and how to interpret it
- Loaded lazily from API (`/academy/chart-help?mode=`)
