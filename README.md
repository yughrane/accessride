# ♿ AccessRide — Accessible City Transport

> A single-file web application for accessible Mumbai suburban rail and metro navigation, built as an HCI (Human-Computer Interface) course project.

---

## Overview

AccessRide is a fully client-side, zero-dependency transit planner for Mumbai's rail and metro network. It covers **150+ stations across 9 lines**, computes real routes with transfers, and is designed ground-up for accessibility — supporting keyboard navigation, screen readers, voice input, GPS, dark mode, high contrast, reduced motion, and variable text sizing.

---

## Getting Started

No installation, no build step, no server required.

```bash
# Just open the file in any modern browser
open accessride.html
```

Or drag `accessride.html` into a browser window. Everything runs locally — the only external calls are:
- Google Fonts (typography)
- OpenStreetMap Nominatim (GPS reverse geocoding — optional, gracefully degrades)

---

## Features

### Journey Planning
- **Source + Destination fields** with smart autocomplete across all 150+ stations
- **Browse by Line** — click any line card to open a station pill picker; tap a station to fill Source then Destination
- **⇅ Swap button** — instantly exchange source and destination
- **Routing engine** — BFS graph traversal finds direct routes and transfer routes across lines, sorted by duration with accessibility-first ordering
- **Up to 3 routes** returned per search, each with expandable step-by-step directions
- **Estimated travel times** calculated from real stop counts and line-specific speeds

### Transit Lines Covered

| Line | Route | Stations |
|------|-------|----------|
| 🚆 Western Line | Churchgate → Dahanu Road | 37 |
| 🚇 Central Main | CSMT → Kalyan Junction | 26 |
| ⚓ Harbour Line | CSMT → Panvel | 25 |
| 🏔️ Kasara Branch | Kalyan → Kasara | 8 |
| 🌿 Karjat Branch | Kalyan → Karjat | 10 |
| 🟢 Metro Line 1 | Versova → Ghatkopar | 12 |
| 🟡 Metro Line 2A | Dahisar East → Andheri West | 11 |
| 🟣 Metro Line 7 | Dahisar East → Gundavali | 17 |
| 🔵 Metro Line 3 (Aqua) | Colaba → SEEPZ | 24 |

### Input Methods
- **Typed input** with live autocomplete (colour-coded by line)
- **Voice input** — 🎙️ button opens a modal with a live interim transcript; speaks Mumbai station names using `en-US` recognition + fuzzy matching
- **GPS** — 📍 button captures device location and reverse-geocodes to the nearest station via OpenStreetMap

### Accessibility Features
- **Dark mode** toggle (🌙 in header and Preferences panel) — light mode is the default
- **High contrast** toggle (◑ in header and Preferences panel)
- **Reduce motion** — kills all CSS animations and transitions via `!important` override
- **Variable text size** — slider from 14px to 24px, live
- **Skip link** — hidden anchor appears on keyboard focus to jump past the header
- **ARIA attributes** — `aria-label`, `aria-pressed`, `aria-expanded`, `aria-live` on all interactive elements
- **Keyboard navigation** — full Tab/Enter/Space/Arrow support including autocomplete dropdown
- **Screen reader** toggle — enables auto-narration of all route results via Web Speech API
- **♿ Accessible filter** — need-chips (Wheelchair / Elevator / Tactile) filter routes to confirmed accessible options; all metro stations auto-qualify
- **Emergency SOS strip** — one-tap alert with visual and audio confirmation

---

## Project Structure

```
accessride.html    ← entire application (HTML + CSS + JS, single file)
README.md
AccessRide_HCI_Features.docx   ← academic HCI feature reference document
```

---

## How the Routing Engine Works

```
User enters Source + Destination
        ↓
Normalize input → look up STATION_INDEX (exact match first)
        ↓
Fuzzy fallback using substring match + Levenshtein distance
        ↓
For each (srcEntry × dstEntry) pair:
  ├── Same line?  → buildDirectRoute()
  └── Different?  → findTransfers(lineA, lineB) → buildTransferRoute()
        ↓
Deduplicate → sort by duration → accessibility-first reorder
        ↓
Return top 3 routes
```

**Transfer stations** (interchanges) are hardcoded — 17 points covering all rail↔rail, rail↔metro, and metro↔metro connections including Dadar (Western + Central + Metro 3), Andheri (Western + Metro 1, 2A, 7), Ghatkopar (Central + Metro 1), Marol Naka (Metro 1 + Metro 3), Dahisar East (Metro 2A + Metro 7), and Churchgate (Western + Metro 3).

**Time estimates:**

| Line type | Min per stop | Transfer buffer | Walk each end |
|-----------|-------------|-----------------|---------------|
| Western / Central / Metro | 3 min | — | 5 min |
| Harbour Line | 4 min | — | 5 min |
| Kasara / Karjat Branch | 5 min | — | 5 min |
| Any transfer route | — | +8 min | 5 min |

---

## Voice Input Notes

Voice uses the browser's **Web Speech API** (`SpeechRecognition`). Works best in **Chrome** and **Edge**. Firefox does not support this API.

- Language is set to `en-US` (broadest browser support; accurately recognises Mumbai station names in Indian English)
- Runs in **continuous mode** with interim results displayed live
- Auto-commits after **800ms of silence**
- Matches heard text against all station names using fuzzy search before setting the field

To use: click the 🎙️ button on either field, speak the station name, and wait for the orb to confirm.

---

## Browser Support

| Browser | Routing | Voice Input | GPS |
|---------|---------|-------------|-----|
| Chrome 90+ | ✅ | ✅ | ✅ |
| Edge 90+ | ✅ | ✅ | ✅ |
| Firefox | ✅ | ❌ (no Speech API) | ✅ |
| Safari 15+ | ✅ | ⚠️ Partial | ✅ |

---

## HCI Principles Applied

This project implements features mapped to:
- **Nielsen's 10 Usability Heuristics** — #1 (status visibility), #3 (user control), #5 (error prevention), #7 (efficiency)
- **WCAG 2.1** — Perceivable (1.4.3, 1.4.4, 1.4.6), Operable (2.1.1, 2.3.3, 2.4.1), Understandable (3.3), Robust (4.1.3)
- **Fitts's Law** — large tap targets, sticky header for frequent controls
- **Hick's Law** — progressive disclosure on route cards, browse-by-line to reduce search space
- **Affordance Theory** — hover lift, press scale, swap rotation, pulse animations
- **Gestalt Continuity** — staggered card entrances, connector lines in step lists

Full feature breakdown: see `AccessRide_HCI_Features.docx`

---

## Course Info

**Course:** Human-Computer Interface  
**Project:** AccessRide — Accessible City Transport  
**Network:** Mumbai Suburban Rail + Metro (Western, Central, Harbour, Kasara, Karjat, Metro 1/2A/7/3)
