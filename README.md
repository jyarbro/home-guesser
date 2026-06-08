# home-guesser

OSM edit heatmap — visualizes a single OpenStreetMap user's changeset history as a heatmap.

## Iterations

| Version | Live preview | Branch |
|---------|-------------|--------|
| iteration1 | [Open](https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration1/index.html) | `iteration1` |
| iteration2 | [Open](https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration2/index.html) | `iteration2` |

## Features

**iteration1**
- Streams all changesets for an OSM username, rendering them to a heatmap as each page loads
- Slide-out controls panel: map tile opacity, heatmap opacity/radius/blur, color scheme selector, hot zone centroids with adjustable threshold and grid size

**iteration2** (adds on top of iteration1)
- Changesets cached in `localStorage` — repeat visits render instantly from cache
- Incremental refresh: only fetches changesets newer than what is already stored
- Username history dropdown with per-user refresh and delete
