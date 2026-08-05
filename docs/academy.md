# BioQuest — Academy Sections

The Academy is the structured learning module for Mediterranean marine species.

## Navigation & Layout
- **Left panel**: Species list with filters
- **Right panel**: Interactive map with observation layers
- **Bottom panel**: Species detail cards (expandable)

## Species Filters

### Taxonomic Groups
Quick-access buttons for major taxonomic groups:
- 🐚 Mollusca (1,014 species)
- 🐟 Actinopterygii (312 species)
- 🌿 Plantae (466 species)
- 🦀 Crustacea (167 species)
- 🪸 Cnidaria (170 species)
- 🧽 Porifera (102 species)

### Family Filter
- Secondary filter within selected taxonomic group
- Button tabs for each family
- Collapsible when many families present

### Difficulty Tiers
- 🥇 **Gold**: Common, distinctive species
- 🥈 **Silver**: Requires attention to detail
- 🥉 **Bronze**: Rare, cryptic, or expert-level

### Search
- Autocomplete with debounced input
- Searches scientific names, common names, Catalan names
- Cross-references Minka + iNaturalist taxonomy APIs

### Rarity Filter
- Species with >100 observations
- Species with 10–100 observations
- Species with <10 observations (rare finds)

## Map Layers (Right Panel)

### Observation Markers
- Colored dots for each species observation
- Click for observation details (photo, date, depth, location)
- Cluster at low zoom levels

### Heatmap Mode
- Density visualization for species with many observations
- Color scale: Blue (low) → Red (high density)

### Legend Panel
- Bottom-left expandable legend
- Shows layer information and color scales

## Species Detail Cards

### Photo Gallery
- Full-resolution images from iNaturalist/Minka
- Swipe/arrow navigation between photos
- Zoom on click

### Identification Card
- Scientific name (WoRMS-validated)
- Common names (Catalan, Spanish, English)
- Taxonomic tree (Kingdom → Phylum → Class → Order → Family → Genus → Species)
- IUCN Red List status badge
- Difficulty tier indicator

### Morphology Section
- AI-generated diagnostic features from GROC/OPK
- Key identification characteristics
- Similar species list with comparison notes

### Ecology Section
- Habitat type (rocky, sandy, seagrass, pelagic)
- Depth range (min–max meters)
- Diet / feeding behavior
- Reproduction notes
- Seasonal patterns

### Distribution Section
- Geographic range map
- Bathymetric profile
- Observation density over time

### Conservation Section
- IUCN threat category
- Population trend
- Major threats
- Protection status

### Edu Progress Tracking
- ✅ Mark species as "learned"
- 📝 Personal notes per species
- ⭐ Favorite/bookmark species
- 📊 Study progress per taxonomic group
