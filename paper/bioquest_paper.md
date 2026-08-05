# BioQuest: Gamified Learning of Mediterranean Marine Biodiversity through AI-Powered Species Identification

**Authors**: Gustavo Zafra (Yespi)
**Repository**: https://github.com/yespi/bioquest
**Live**: https://bioquest.yespi.es

## Abstract

BioQuest is an educational platform that gamifies learning Mediterranean marine species identification. Built on the YOLOFauna AI engine, it combines a structured academy of 1,369 species, adaptive quizzes with AI-generated distractors, real EUMETSAT satellite imagery for environmental context, and an achievement system inspired by game design principles. The platform targets diving schools, marine biology students, and citizen scientists, creating a pipeline from learning (BioQuest) to contributing (FotoFauna) to improving the AI (YOLOFauna). This paper describes the platform's design, pedagogical foundations, technical implementation, and the integration of AI-powered features for educational purposes.

## 1. Introduction

Learning to identify marine species is a complex cognitive task requiring memorization of hundreds of scientific names, recognition of subtle morphological differences, and understanding of ecological context. Traditionally taught through field guides and in-person mentorship, this skill is increasingly scarce as professional taxonomists decline in number (Hopkins & Freckleton, 2002).

Digital learning platforms offer advantages over traditional methods: adaptive difficulty, immediate feedback, multimedia integration, and progress tracking. BioQuest leverages these capabilities alongside the same AI engine used for species identification (YOLOFauna) to create an immersive, gamified learning environment.

## 2. Platform Architecture

### 2.1 Shared Infrastructure

BioQuest shares its core infrastructure with FotoFauna:
- Same PostgreSQL database
- Same YOLOFauna AI engine for identification
- Same species catalog (1,369 species, WoRMS-validated)
- Same photo repository (525,253 images)
- Same authentication system (Google OAuth)

### 2.2 Frontend

Single-page application with:
- Offline-capable species flashcards (Service Worker caching)
- OpenStreetMap-based exploration maps
- WebP-optimized image delivery
- Responsive design for mobile/tablet/desktop
- Catalan/Spanish/English interface

## 3. Species Academy

### 3.1 Structured Curriculum

Species are organized by:
- **Taxonomic groups**: Mollusca (1,014), Plantae (466), Actinopterygii (312), Cnidaria (170), Crustacea (167), Porifera (102), Echinodermata (54)
- **Difficulty tiers**: Gold (common/distinctive), Silver (requires attention), Bronze (rare/cryptic)
- **Ecological zones**: Intertidal, infralittoral, circalittoral, pelagic

### 3.2 Species Cards

Each card displays:
- Gallery of representative photographs
- Scientific name with WoRMS-validated taxonomy
- Common names (Catalan, Spanish, English)
- AI-generated diagnostic features from GROC/OPK field guides
- Similar species list with comparison notes
- Depth range, habitat type, seasonality
- IUCN Red List conservation status
- Distribution map (GBIF occurrence data)

### 3.3 Learning Modes

**Browse Mode**: Free exploration of the catalog with search and filter capabilities.

**Learning Mode**: Structured path through tiers with:
- Spaced repetition following the Ebbinghaus forgetting curve
- Species reappear at increasing intervals (1, 3, 7, 30 days)
- Progress tracking per taxonomic group
- Adaptive difficulty based on performance

### 3.4 Filters and Search

- Quick-access buttons for major taxonomic groups
- Family-level secondary filters
- Difficulty tier selector
- Rarity filter (observation count ranges)
- Autocomplete search across all names

## 4. Interactive Quizzes

### 4.1 Question Types

**Species Identification**: "What species is this?" with photograph and multiple choice.

**Feature Recognition**: "Which of these has tuberculate rhinophores?" Tests morphological knowledge.

**Habitat Matching**: "On which substrate would you find X?" Tests ecological knowledge.

**Taxonomic Ranking**: "Arrange these taxa from species to phylum." Tests hierarchical understanding.

**Similar Species**: "Which of these is most easily confused with X?" Tests comparative knowledge.

### 4.2 AI-Generated Distractors

The YOLOFauna AI engine generates quiz distractors by:
1. Locating the correct species in BioCLIP embedding space
2. Finding the 3-4 most visually similar species (cosine similarity)
3. Using these as multiple-choice options
4. Deriving distractors from the 815 cryptic pairs dataset

This ensures questions are appropriately challenging — distractors are genuinely confusable species, not random ones.

### 4.3 Adaptive Difficulty

As user accuracy increases:
- Distractors become more visually similar
- Taxonomic scope narrows (e.g., "Which Flabellina species?")
- Timer-based challenge mode activates
- Question types mix to prevent pattern recognition

## 5. FaunaDex (Explore)

### 5.1 Species Discovery

The FaunaDex is a gamified species collection system:
- Interactive Mediterranean map with observation markers
- Click markers to "discover" new species
- Species added to personal collection
- Color-coded by taxonomic group
- Rarity shown by marker size and glow effect

### 5.2 Collection Progress

- Counter: "Discovered X of 1,369 species"
- Progress bar with visual completion indicator
- Breakdown by taxonomic category
- Recent discoveries list

### 5.3 Medals and Awards

**Award Types:**
- Species milestones: 10, 50, 100, 250, 500, 1000
- Taxonomic mastery: Complete all Mollusca, Fish, etc.
- Tier completion: All Gold, Silver, Bronze
- Discovery streaks: Days with >=1 new species
- Rare finds: Species with <10 total observations

**Award Display:**
- Awards badge (top-right corner)
- Full-screen medal overlay
- Special markers on FaunaDex map
- Animated HUD medal name
- Toast notifications on unlock

## 6. Atlas: Satellite Exploration

### 6.1 EUMETSAT Integration

Real satellite imagery from Meteosat Third Generation (MTG-I1):

**True Colour RGB**: Visible-spectrum satellite imagery showing clouds, land, and sea as seen from space. Updated every 10 minutes at ~2 km/pixel resolution.

**Dust RGB**: False-color composite for atmospheric analysis. Highlights Saharan dust storms crossing the Mediterranean, cloud types, and aerosol concentrations.

**Geocolour** (fallback): Enhanced-colour imagery when True Colour is unavailable.

### 6.2 Animation System

- 30 frames covering ~5 hours of data
- Play/pause/step controls
- Smooth transitions using setParams (not removeLayer)
- Auto-advancing with configurable speed

### 6.3 Oceanographic Layers

**Sea Surface Temperature (SST)**:
- Thermal infrared measurements
- Shows temperature gradients and fronts
- Blue (cold) to Red (warm) color scale

**Chlorophyll-a Concentration**:
- Phytoplankton abundance proxy
- Indicates productive waters
- Purple (low) to Green (high) color scale

**Wave Height & Direction**:
- Significant wave height from meteorological models
- Direction arrows for swell propagation
- Useful for dive condition assessment

**Water Transparency/Turbidity**:
- Water clarity measurements
- Affected by sediment, plankton, river discharge

### 6.4 Map Layers

- OpenStreetMap base layer
- EUMETSAT overlay with opacity control
- Species observation markers (GBIF/Minka)
- Bathymetry contours (GEBCO)
- Marine Protected Areas
- City labels at appropriate zoom

### 6.5 Performance Optimization

- JPEG tiles at 128px (4 KB vs 122 KB PNG, 28x faster)
- Nginx caching with proxy_ignore_headers Set-Cookie
- Tile preloading when atlas opens
- Toast notification during initial load

## 7. Conservation Education

### 7.1 IUCN Red List Integration

- Species conservation status from IUCN API
- All categories: LC, NT, VU, EN, CR
- Population trend data
- Threat categories and descriptions

### 7.2 Protected Species

- Visual highlighting for threatened species
- Educational notes on conservation measures
- Links to IUCN species pages
- Special maps for endangered species (MITECO data)

### 7.3 Fishing Effort Map

- Shows fishing activity intensity in the Mediterranean
- Helps learners understand human pressures on marine ecosystems
- Data from Global Fishing Watch

## 8. Diving Conditions

### 8.1 Real-Time Marine Weather

- Wave height and direction
- Wind speed and direction
- Water temperature
- Visibility estimates
- Tide tables

### 8.2 Dive Site Assessment

- Color-coded conditions (green/yellow/red)
- Exposure analysis (protected from current wind/swell)
- Best time to dive recommendations
- Integration with local dive site databases

## 9. Achievement System

### 9.1 Badge Design

Each achievement type has a unique visual design:
- Species milestones: Gold star with count
- Taxonomic mastery: Group-specific icon (shell, fish, coral)
- Streaks: Flame icon with day count
- Rare finds: Diamond with glow effect

### 9.2 Progression Mechanics

- Linear progression for species counts
- Mastery requirements for taxonomic groups
- Multiplicative scoring for streaks
- Bonus points for rare species

### 9.3 Social Features

- Leaderboards by species count and quiz accuracy
- Peer comparison within diving schools
- Shareable achievement cards
- Community challenges

## 10. User Progress Tracking

### 10.1 Learning Analytics

- Species viewed and quizzed
- Accuracy over time
- Time spent per taxonomic group
- Weak areas identified for focused review

### 10.2 Visualization

- Radar charts by taxonomic group
- Calendar heatmap of learning activity
- Progress bars for tier completion
- Comparative stats vs. community average

## 11. Educational Design

### 11.1 Pedagogical Foundations

BioQuest's design draws on established learning principles:
- **Spaced repetition** (Ebbinghaus, 1885): Species reappear at optimal intervals
- **Test-enhanced learning** (Roediger & Karpicke, 2006): Active recall through quizzes
- **Interleaving** (Rohrer & Taylor, 2007): Mixed taxonomic groups prevent pattern recognition
- **Dual coding** (Paivio, 1986): Visual (photos) + verbal (descriptions) information

### 11.2 AI-Enhanced Pedagogy

The YOLOFauna AI engine provides unique educational capabilities:
- **Automated distractor generation**: Creates appropriately challenging quiz options
- **Real-time feedback**: Verifies user identifications against AI predictions
- **Visual similarity metrics**: Quantifies how confusable two species are
- **Adaptive difficulty**: Adjusts challenge based on embedding-space distances

### 11.3 Target Audiences

- Diving schools: Pre-dive briefings, post-dive species logging
- Universities: Marine biology curricula, field course preparation
- Citizen scientists: Training for platforms like Minka and iNaturalist
- General public: Ecotourism, aquariums, nature enthusiasts

## 12. FotoFauna Integration

### 12.1 Learning-to-Contributing Pipeline

Users who achieve proficiency in BioQuest can graduate to contributing on FotoFauna:
- Quiz accuracy threshold (configurable)
- Minimum species count learned
- Gradual introduction to real-world observations
- Mentored mode with AI assistance

### 12.2 Data Synergy

- Observations from FotoFauna feed into BioQuest's species distribution maps
- Curator confirmations improve AI accuracy, which improves quiz quality
- Learning progress in BioQuest can qualify users for advanced identification tasks

## 13. Preliminary Results

### 13.1 Content Coverage

| Metric | Value |
|--------|-------|
| Species in academy | 1,369 |
| With photographs | 1,369 |
| With AI descriptions | 32 |
| With IUCN status | ~200 |
| Quiz question pairs (cryptic) | 815 |

### 13.2 Platform Status

BioQuest launched in July 2026. Usage statistics are preliminary but the platform has demonstrated:
- Functional academy with all 1,369 species
- Working quiz system with AI distractors
- EUMETSAT satellite integration
- Achievement and medal system
- Seamless sharing with FotoFauna

## 14. Discussion

### 14.1 AI as Educational Tool

BioQuest demonstrates that the same AI used for species identification can be repurposed for education. The embedding space that separates species for classification also identifies which species are visually confusable — directly informing quiz difficulty and learning priorities.

### 14.2 The Education-Citizen Science Pipeline

The pathway from BioQuest learner to FotoFauna contributor creates a sustainable model:
1. Learn species in BioQuest (motivated by gamification)
2. Contribute identifications on FotoFauna (real-world application)
3. AI improves from curator feedback (better identifications)
4. Better AI means better learning tools (virtuous cycle)

### 14.3 Future Work

- Formal learning outcome evaluation (pre/post tests)
- Mobile-optimized experience for field use
- English and other Mediterranean language support
- Integration with university curricula
- Expanded morphological descriptions from taxonomic literature

## 15. Conclusion

BioQuest reimagines marine species learning for the digital age. By combining AI-powered species identification with gamification, satellite imagery, and structured pedagogy, it creates an engaging alternative to traditional field guides. The pipeline from learning to contributing to improving the AI represents a holistic approach to biodiversity education and citizen science.

## References

1. Zafra, G. (2026). YOLOFauna: Fine-tuning BioCLIP for Mediterranean Marine Species Identification. In preparation.
2. Zafra, G. (2026). FotoFauna: A Citizen Science Platform. In preparation.
3. Stevens, S. et al. (2024). BioCLIP. CVPR 2024.
4. Hopkins, G.W. & Freckleton, R.P. (2002). Declines in taxonomists. Animal Conservation.
5. Roediger, H.L. & Karpicke, J.D. (2006). Test-Enhanced Learning. Psychological Science.
6. Ebbinghaus, H. (1885). Memory: A Contribution to Experimental Psychology.
7. Rohrer, D. & Taylor, K. (2007). The shuffling of mathematics problems improves learning. Instructional Science.
8. Paivio, A. (1986). Mental Representations: A Dual Coding Approach. Oxford University Press.
9. EUMETSAT (2026). Meteosat Third Generation imagery.

---

*Paper in preparation. Version 2026-08-05.*
