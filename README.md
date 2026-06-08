# home-guesser

OSM edit heatmap — visualizes a single OpenStreetMap user's changeset history as a heatmap.

## Iterations

| Version | Live preview | Branch |
|---------|-------------|--------|
| iteration1 | [Open](https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration1/index.html) | `iteration1` |
| iteration2 | [Open](https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration2/index.html) | `iteration2` |
| iteration3 | [Open](https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration3/index.html) | `iteration3` |
| iteration4 | [Open](https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration4/index.html) | `iteration4` |

## Features

**iteration1**
- Streams all changesets for an OSM username, rendering them to a heatmap as each page loads
- Slide-out controls panel: map tile opacity, heatmap opacity/radius/blur, color scheme selector, hot zone centroids with adjustable threshold and grid size

**iteration2** (adds on top of iteration1)
- Changesets cached in `localStorage` — repeat visits render instantly from cache
- Incremental refresh: only fetches changesets newer than what is already stored
- Username history dropdown with per-user refresh and delete

**iteration3** (mobile fixes on top of iteration2)
- Fixes black map on iOS/Android: `100dvh` prevents browser-chrome overlap; `position: absolute` map fill ensures Leaflet always gets a real container size
- Mobile toolbar wraps into two rows (title + Controls / input + Search) with 44px touch targets
- Controls panel expands to full width on mobile instead of a 264px sliver
- Input `font-size: 16px` on mobile prevents iOS auto-zoom on focus
- Keyboard dismissed automatically when a search starts
- `map.invalidateSize()` called after the controls panel slide animation

**iteration4** (debug console + robust networking on top of iteration3)
- Per-request 15 s timeout via `timedFetch()` — no more silent hangs on restricted networks
- Removed blocking connectivity pre-check; failures surface as clear error messages instead
- JavaScript debug console modal (toolbar terminal icon): captures all `console.*` output from page load onward, color-coded by level, error badge count, Clear and Copy buttons
- CSP violation detection via `securitypolicyviolation` event — catches htmlpreview.github.io blocking OSM API calls
- Unhandled promise rejections captured in the debug log
- Strategic `console.log` around each OSM API fetch (URL, HTTP status, changeset count)
