# BioQuest — Feature Documentation

## Table of Contents
1. [Species Academy](#1-species-academy)
2. [Interactive Quizzes](#2-interactive-quizzes)
3. [Satellite Exploration (Atlas)](#3-satellite-exploration-atlas)
4. [Diving Conditions](#4-diving-conditions)
5. [Conservation Status (IUCN)](#5-conservation-status-iucn)
6. [Achievement System](#6-achievement-system)
7. [User Progress Tracking](#7-user-progress-tracking)
8. [FotoFauna Integration](#8-fotofauna-integration)

## 1. Species Academy

### 1.1 Structured Curriculum
1,369 Mediterranean marine species organized by:
- **Taxonomic groups**: Mollusca (1,014), Plantae (466), Actinopterygii (312), Cnidaria (170), Crustacea (167), Porifera (102), Echinodermata (54)
- **Difficulty tiers**: Gold (common/distinctive), Silver (requires attention), Bronze (rare/cryptic)
- **Ecological zones**: Intertidal, infralittoral, circalittoral, pelagic

### 1.2 Species Cards
Each card displays:
- Gallery of representative photographs (from iNaturalist/Minka, 525K total)
- Scientific name with WoRMS-validated taxonomy
- Common names (Catalan, Spanish, English)
- **AI-generated diagnostic features**: key morphological traits extracted from GROC/OPK field guides
- Similar species list (for comparison learning)
- Depth range, habitat type, seasonality
- IUCN Red List conservation status (when available)
- Distribution map (GBIF occurrence data)

### 1.3 Learning Mode vs Browse Mode
- **Browse**: Free exploration of the catalog
- **Learning**: Structured path through tiers, with progress tracking and spaced repetition

### 1.4 Thumbnail System
Nightly cron job pre-caches WebP thumbnails (200px, 400px, 800px) for all academy species from iNaturalist research-grade observations. Instant loading on slow connections.

## 2. Interactive Quizzes

### 2.1 Question Types
- **Species Identification**: "What species is this?" with photograph and multiple choice
- **Feature Recognition**: "Which species has tuberculate rhinophores?"
- **Habitat Matching**: "Where would you find X species?"
- **Taxonomic Ranking**: "Arrange these taxa from species to phylum"
- **Similar Species**: "Which of these is most similar to X?"

### 2.2 AI-Generated Distractors
The YOLOFauna AI engine generates quiz distractors by:
1. Finding the correct species in embedding space
2. Identifying the 3-4 most visually similar species (cosine similarity in BioCLIP embedding space)
3. Using those as multiple-choice options (creating appropriately challenging questions)

This is derived from the 815 cryptic pairs dataset.

### 2.3 Adaptive Difficulty
- Questions get harder as user accuracy increases
- Distractors become more visually similar to the correct answer
- Taxonomic scope narrows (e.g., "Which Flabellina species?")
- Timer-based challenge mode

### 2.4 Spaced Repetition
Previously learned species reappear at increasing intervals (1 day, 3 days, 7 days, 30 days) following the Ebbinghaus forgetting curve.

## 3. Satellite Exploration (Atlas)

### 3.1 EUMETSAT Integration
Real satellite imagery from EUMETSAT's Meteosat Third Generation (MTG-I1):
- **True Colour RGB** (`mtg_fd:rgb_truecolour`): Visible-spectrum imagery
- **5-hour animations**: 30 frames at 10-minute intervals
- **Update frequency**: New data every 15 minutes
- **Resolution**: 2km/pixel (Mediterranean-wide)

### 3.2 Map Layers
- OpenStreetMap base layer
- EUMETSAT overlay with opacity control
- Species observation markers (from GBIF/Minka)
- Bathymetry contours
- Marine protected areas

### 3.3 Environmental Context Features
- Sea surface temperature patterns
- Chlorophyll concentration (primary productivity)
- River plume tracking
- Algal bloom detection
- Thermal front identification

### 3.4 Technical Implementation
- JPEG tiles at 128px (4 KB vs 122 KB PNG, 28x faster)
- Nginx caching with `proxy_ignore_headers Set-Cookie` (HIT vs MISS)
- Precarga de tiles al abrir el módulo
- `setParams` en lugar de `removeLayer` para animaciones sin flicker

## 4. Diving Conditions

### 4.1 Real-time Marine Weather
Integration with meteorological APIs for:
- Wave height and direction (significant wave height)
- Wind speed and direction
- Water temperature
- Visibility estimates
- Tide tables

### 4.2 Dive Site Assessment
- Color-coded conditions (green/yellow/red)
- Exposure notes (which dive sites are protected from current wind/swell)
- Best time to dive recommendations

## 5. Conservation Status (IUCN)

### 5.1 IUCN Red List Integration
- Species conservation status from IUCN API
- Categories: LC (Least Concern), NT (Near Threatened), VU (Vulnerable), EN (Endangered), CR (Critically Endangered)
- Population trend data
- Threat categories

### 5.2 Protected Species Highlighting
- Species with conservation concern are visually highlighted
- Educational notes on threats and conservation measures
- Links to IUCN species pages

## 6. Achievement System

### 6.1 Badge Types
- **Species Milestones**: 10, 50, 100, 500, 1000 species learned
- **Tier Mastery**: Complete all species in Gold/Silver/Bronze
- **Taxonomic Specialization**: Master all Mollusca, all Fish, etc.
- **Streak Tracking**: Consecutive days of learning
- **Quiz Performance**: 100% accuracy streaks

### 6.2 Community Features
- Leaderboards (species count, quiz accuracy)
- Peer comparison within diving schools
- Shareable achievement cards

## 7. User Progress Tracking

### 7.1 Learning History
- Species viewed and quizzed
- Accuracy over time
- Time spent per taxonomic group
- Weak areas identified for focused review

### 7.2 Progress Visualization
- Radar charts by taxonomic group
- Calendar heatmap of learning activity
- Progress bars for tier completion

## 8. FotoFauna Integration

### 8.1 Learning → Contributing Pipeline
Users who achieve high quiz accuracy can "graduate" to contributing real identifications on FotoFauna:
- Quiz accuracy threshold (configurable)
- Minimum species count learned
- Gradual introduction to real observations

### 8.2 Shared Infrastructure
- Same PostgreSQL database
- Same YOLOFauna AI engine
- Same user authentication (Google OAuth)
- Same species catalog and photo repository
