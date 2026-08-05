# BioQuest — Atlas: Satellite Exploration Maps

## Overview
The Atlas displays real satellite imagery and oceanographic data over an interactive map (Leaflet/OpenStreetMap). It helps learners understand how environmental conditions influence marine biodiversity.

## Map Layers

### 1. 🛰️ True Colour RGB (Meteosat MTG-I1)
**Layer ID**: `mtg_fd:rgb_truecolour`
- Real visible-spectrum satellite imagery from EUMETSAT's Meteosat Third Generation
- Shows clouds, land, sea as seen from space
- **Update frequency**: New image every 10 minutes
- **Resolution**: ~2 km/pixel
- **Fallback**: `mtg_fd:rgb_geocolour` (enhanced colour) when True Colour unavailable

### 2. 🌡️ Sea Surface Temperature (SST)
- Thermal infrared measurements
- Shows water temperature gradients
- Useful for identifying thermal fronts (where warm and cold water meet — biodiversity hotspots)
- **Units**: °C
- **Color scale**: Blue (cold) → Red (warm)

### 3. 🟢 Chlorophyll-a Concentration
- Measures phytoplankton abundance (primary productivity)
- High chlorophyll = productive waters = more marine life
- Shows coastal upwelling zones, river plume dispersion
- **Units**: mg/m³
- **Color scale**: Purple (low) → Green (high)

### 4. 🌊 Wave Height & Direction
- Significant wave height from meteorological models
- Direction arrows show swell propagation
- **Units**: meters
- Useful for assessing dive conditions and coastal exposure

### 5. 🔵 Water Transparency / Turbidity
- Measures water clarity
- Affected by sediment, plankton blooms, river discharge
- **Color scale**: Brown (turbid) → Blue (clear)

### 6. 🌬️ Wind Speed & Direction
- Surface wind vectors
- Particle animation showing wind flow
- **Units**: km/h or knots
- **Source**: `bq-wind-particles.js` with animated canvas overlay

### 7. 🏔️ Bathymetry
- Seafloor depth contours
- Shows underwater canyons, seamounts, continental shelf
- **Source**: GEBCO global bathymetry
- Depth zones color-coded

### 8. 🧂 Sea Surface Salinity
- Variations in ocean salinity
- Freshwater input detection (river mouths, rain)
- **Units**: PSU

### 9. 🔴 Dust RGB (Atmospheric)
**Layer ID**: `mtg_fd:rgb_dust`
- False-color composite for atmospheric analysis
- Highlights dust storms (Sahara → Mediterranean)
- Shows cloud types and aerosol concentrations

### 10. 🌿 NDVI (Vegetation Index)
- Coastal vegetation health
- Useful for wetland and seagrass monitoring
- **Color scale**: Brown (bare) → Green (dense vegetation)

## Controls & Features

### Animation
- **30 frames** (~5 hours of data at 10-min intervals)
- Play/pause/step controls
- Smooth transitions using `setParams` instead of `removeLayer` to prevent flicker

### Time Display
- Current satellite time shown in top bar
- Auto-updates every 10 minutes with new satellite data

### Zoom Levels
- **Max zoom**: 10 (satellite imagery)
- **Min zoom**: 3 (global view)
- Labels appear for cities/bathymetry at appropriate zoom

### Performance
- JPEG tiles at 128px (4 KB vs 122 KB PNG, **28× faster**)
- Nginx caching with `proxy_ignore_headers Set-Cookie` (cache HIT instead of MISS)
- Tile preloading when atlas opens
- Toast notification during initial load

### City Labels
- Mediterranean coastal cities overlaid at zoom ≥ 6
- Dynamic positioning based on map bounds
- Temperature display removed for performance

## Special Maps

### Conservation Areas Map
- Marine Protected Areas (MPAs) in the Mediterranean
- IUCN protected area categories
- National park boundaries

### Endangered Species Map (MITECO)
- Spanish threatened species distribution
- Red List categories overlaid on map
- Click for species details

### Sampling Effort Map
- Shows where observations have been recorded
- Helps identify under-sampled areas
- Density heatmap visualization

## Split Map (50/50 Comparison)
- Compare distribution of 2 species side by side
- Synchronized pan and zoom
- Species selector with autocomplete search
- Each panel independently configurable
