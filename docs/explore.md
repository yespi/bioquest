# BioQuest — FaunaDex (Explore)

The Explore section ("FaunaDex") is a gamified species discovery and collection system inspired by the Pokédex concept.

## FaunaDex Map

### Interactive Discovery Map
- **Mediterranean-wide map** with observation points
- Each marker = a species occurrence
- Click a marker to "discover" a new species
- Species added to your personal collection
- Color-coded by taxonomic group

### Discovery Mechanics
- Species near your current location shown first
- Zoom to explore different regions
- Pan across the Mediterranean to find different species
- Rarity shown by marker size and glow effect

### Collection Progress
- **Counter**: "Discovered 142 of 1,369 species"
- **Progress bar**: Visual completion indicator
- **By group**: Breakdown by taxonomic category
- **Recent discoveries**: Last 5 species found

## Medals & Awards System

### Award Types
- 🥇 **Species Milestones**: 10, 50, 100, 250, 500, 1000
- 🎯 **Taxonomic Mastery**: Complete all Mollusca, Fish, etc.
- 🏆 **Tier Completion**: All Gold, Silver, Bronze species
- 🔥 **Discovery Streaks**: Days with at least 1 new species
- 💎 **Rare Finds**: Discover species with <10 total observations

### Award Display
- **Awards badge** (top-right corner): Shows medal count
- **Award overlay**: Full-screen modal with all earned medals
- **Award POIs**: Special markers on FaunaDex map for medal locations
- **HUD medal name**: Animated display when near an award POI
- **Toast notification**: "New medal earned!" popup

### Visual Design
- Gold border with glow effect
- Star icon for major milestones
- Pulse animation on newly earned medals
- Sound effect on achievement unlock

## Map Controls

### Layer Inspector
- Toggle between different data layers
- URL hash reflects current state (shareable links)
- Tab-based switching: Atlas / Observations / Conservation

### City Labels
- Mediterranean coastal city names at appropriate zoom
- Barcelona, Valencia, Palma, Marseille, Genoa, Napoli, etc.
- Dynamic rendering based on map bounds and zoom level

### Zoom & Pan
- Scroll to zoom, drag to pan
- Geolocation button (find my location)
- Reset view button (Mediterranean overview)
- Smooth animations between views

## Mobile Adaptation
- Full-screen map with overlay controls
- Bottom sheet for species details
- Touch-friendly markers (larger hit area)
- Safe area insets for notched phones
