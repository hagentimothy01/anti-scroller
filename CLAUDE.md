# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**"The Daily"** is a single-file RSS feed aggregator (`digest_live.html`) — a client-side newspaper-style web app with no build process, no backend, and no dependencies beyond CDN-loaded Google Fonts.

## Running Locally

Open `digest_live.html` directly in a browser, or serve it:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/digest_live.html
```

## Architecture

Everything lives in `digest_live.html`:

- **CSS (lines ~10–363)**: Design system using CSS variables. Fonts: Bebas Neue (display) and DM Sans (body) via Google Fonts. Key color: `#1f5c3a` (green).
- **HTML (lines ~364–423)**: Three horizontal-scrolling card sections — News, Clothes, Music.
- **JavaScript (lines ~425–712)**: All logic in vanilla JS.

### Data Flow

```
SOURCES config (hardcoded RSS URLs)
  → fetchFeed() tries rss2json.com, falls back to allorigins.win
  → parsed into items (title, link, date, summary, thumbnail)
  → renderStory() or renderAlbum() (Pitchfork-specific, includes score/artwork)
  → DOM insertion into section rows
  → setupScroll() adds horizontal scroll hints
  → auto-refresh every 30 minutes
```

### Key JS Functions

| Function | Purpose |
|---|---|
| `fetchViaRss2Json()` / `fetchViaAllOrigins()` | Dual CORS proxy fetchers |
| `fetchFeed()` | Wrapper; tries primary then fallback |
| `parseXml()` | Manual DOMParser-based XML parser for allorigins fallback |
| `renderStory()` | Generic card for news/clothes |
| `renderAlbum()` | Pitchfork-specific card with artwork and score |
| `loadSection()` | Fetches and interleaves items from multiple sources |
| `loadAll()` | Runs all sections in parallel |

### Adding/Changing RSS Sources

Edit the `SOURCES` object near the top of the `<script>` block:

```javascript
const SOURCES = {
  news: [{ label: 'Label', url: 'https://...' }],
  clothes: [...],
  music: [...]
};
```

Pitchfork is the only source with special rendering (`renderAlbum`); all others use `renderStory`.
