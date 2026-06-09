# home-guesser — Claude Code project notes

## Repo structure

```
home-guesser/
  .bare/          ← bare git repo (all objects + refs live here)
  .git            ← file containing "gitdir: ./.bare" (routes git commands)
  iteration1/     ← worktree for branch `iteration1`
  iteration2/     ← worktree for branch `iteration2`
  iteration3/     ← worktree for branch `iteration3`
  iteration4/     ← worktree for branch `iteration4`
  iteration5/     ← worktree for branch `iteration5`
  iteration6/     ← worktree for branch `iteration6`
  iteration7/     ← worktree for branch `iteration7`
  CLAUDE.md       ← on branch `main`
```

- The root directory is the `main` branch worktree (shared docs only, no app code).
- Each iteration lives in its own branch and worktree subfolder.
- `main` holds only `CLAUDE.md`.

## Convention: new major changes → new branch + worktree

Never modify an existing iteration in place for a significant feature.  
Instead:
1. Create a new branch from the previous iteration: `git branch iterationN iterationN-1`
2. Add a worktree: `git worktree add iterationN iterationN`
3. Do all work inside `iterationN/`
4. **Always** update the `gh-pages` landing page (`gh-pages/index.html`): add a new card at the top for iterationN and remove "(latest)" from the previous top card. Also copy `iterationN/index.html` into the `gh-pages` worktree so the live URL resolves.
5. Add `iterationN/` to `.gitignore` on `main` so the worktree dir doesn't show as untracked.
6. Update `CLAUDE.md` on `main` with the new iteration's feature summary and live preview link.
7. Commit and push: the `iterationN` branch, the `gh-pages` branch, and the `main` branch.

Even small bug fixes that affect correctness should go into a new iteration — err on the side of incrementing.

## Change history

### iteration1 — OSM edit heatmap (streaming)
- Initial single-page static HTML app: Leaflet.js map + Leaflet.heat heatmap
- OSM Changeset API pagination via async generator; streams pages as they arrive
- Heatmap updates live with `heatLayer.setLatLngs()` as each page lands
- Slide-out controls panel: map tile opacity, heatmap opacity/radius/blur, color scheme selector
- Hot zone centroids: grid-based clustering, adjustable threshold and grid size, circle markers sized by `sqrt(count)`
- Mobile layout fix: `flex-wrap` + `order` media query so input stays visible on narrow screens
- Subtle loading bar: 3px gradient strip at top of map, animates in on search start, fades out on completion

### iteration2 — localStorage caching + user history
- All iteration1 features, plus:
- Changesets stored in `localStorage` keyed by username; repeat visits render instantly from cache
- Incremental refresh: walks newest pages until a fully-known-ID page is hit, then stops — never re-fetches old data
- Username history dropdown: previously searched users listed, each with a refresh button and delete button
- AbortController cancels in-flight fetches when a new search starts
- Mobile fix updated to wrap `#username-wrap` (input + dropdown container) as a unit

### iteration3 — mobile layout overhaul + search drawer
- Fixes black map on iOS/Android: `100dvh` + `position: absolute; inset: 0` on `#map`
- User icon button in toolbar opens a slide-down search drawer; closes after search starts
- 3px loading bar across toolbar bottom animates with CSS keyframes (`left` property, not `transform`, for Safari clip compat)
- Loading spinner on user icon during fetch; per-page status updates in status bar
- In-map error overlay for network/404/rate-limit failures
- 44px touch targets, `font-size: 16px` on input (prevents iOS auto-zoom), full-width controls panel on mobile

### iteration4 — debug console + robust networking
- `timedFetch()` wraps every OSM API call with a 15 s timeout — no silent hangs on restricted networks
- Removed blocking `checkConnectivity()` pre-check; `classifyError()` handles `TimeoutError` and CSP violations
- Console capture script in `<head>` records all `console.*` output from page load onward
- `securitypolicyviolation` event listener logs CSP blocks (e.g. htmlpreview.github.io blocking cross-origin fetch)
- `unhandledrejection` listener captures async failures
- Debug console modal (terminal icon in toolbar): color-coded log, error badge, Clear + Copy (with `execCommand` fallback)
- Strategic `console.log` around each `changesetPages` fetch: URL, HTTP status, changeset count

### iteration6 — dark map tiles for heatmap contrast
- Default map tile style is "Dark" (`grayscale(1) invert(1) brightness(0.7)` CSS filter on tile pane) — heatmap colors now pop against a neutral dark background instead of competing with OSM's light-blue water tiles
- "Map style" dropdown in Controls panel: Dark (default), Grayscale, Light
- Filter applied directly to `map.getPanes().tilePane.style.filter` — zero extra requests, works with any tile source

### iteration7 — centroid hotspot placement
- Replaces grid-cell-center markers with true data centroids: each hot zone's marker is placed at the weighted mean lat/lon of all changesets in that zone
- Flood-fill consolidation: adjacent grid cells that both pass the threshold are merged into a single cluster; only one centroid per connected region (prevents marker clutter in dense areas)
- Outline-style circle markers: thin cyan stroke with near-transparent fill so the centroid sits clearly over the heatmap without obscuring it
- Tooltip shows total changeset count and number of merged cells per cluster
- Threshold slider still adjustable; grid size still adjustable — both re-cluster in real time

### iteration5 — pagination fix
- Fixed infinite loop in `changesetPages`: `time=T1` (OSM API means "closed after T1") caused the same page to be re-fetched forever; changed to `time=epoch,T1` range format to page backwards through history
- Uses `closed_at` (with `created_at` fallback) as the cursor, matching the OSM API sort order
- Also fixes user history not being saved (saveUser/touchHistory were unreachable while the generator looped)

## To-do / potential next iterations

### iteration3 ideas
- **Date range filter** — restrict heatmap to changesets within a user-selected date window; use the `time=from,to` OSM API parameter
- **Click-to-inspect changesets** — clicking a hot zone shows a sidebar with the N most-edited changesets in that cell (OSM changeset URL, comment, timestamp)
- **Multi-user overlay** — search two users simultaneously, render each as a different color heatmap on the same map
- **Tag-based filtering** — fetch changeset tags and let the user filter by edit type (roads, buildings, POIs, etc.)
- **Export** — download current heatmap points as GeoJSON or PNG snapshot

### Quality / polish
- Add a "clear cache" button in the history dropdown to wipe all stored data at once
- Show total changeset count and date range in the UI once loading completes
- Persist control panel settings (opacity, radius, color scheme) to `localStorage` so they survive page refresh
- Error state for unknown usernames or API failures (currently silent)

## Live previews (requires public repo)

| Version | URL |
|---------|-----|
| iteration1 | https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration1/index.html |
| iteration2 | https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration2/index.html |
| iteration3 | https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration3/index.html |
| iteration4 | https://htmlpreview.github.io/?https://github.com/jyarbro/home-guesser/blob/iteration4/index.html |
| iteration5 | https://yarbro.me/home-guesser/iteration5/ |
| iteration6 | https://yarbro.me/home-guesser/iteration6/ |
| iteration7 | https://yarbro.me/home-guesser/iteration7/ |
