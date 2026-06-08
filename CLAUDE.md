# home-guesser — Claude Code project notes

## Repo structure

```
home-guesser/
  .bare/          ← bare git repo (all objects + refs live here)
  .git            ← file containing "gitdir: ./.bare" (routes git commands)
  iteration1/     ← worktree for branch `iteration1`
  iteration2/     ← worktree for branch `iteration2`
  README.md       ← on branch `main`
  CLAUDE.md       ← on branch `main`
```

- The root directory is the `main` branch worktree (shared docs only, no app code).
- Each iteration lives in its own branch and worktree subfolder.
- `main` holds only `README.md` and `CLAUDE.md`.

## Convention: new major changes → new branch + worktree

Never modify an existing iteration in place for a significant feature.  
Instead:
1. Create a new branch from the previous iteration: `git branch iterationN iterationN-1`
2. Add a worktree: `git worktree add iterationN iterationN`
3. Do all work inside `iterationN/`
4. **Always** update `README.md` on `main` with the new iteration's live preview link and feature summary.
5. Add `iterationN/` to `.gitignore` on `main` so the worktree dir doesn't show as untracked.
6. Commit and push both the `iterationN` branch and the `main` branch.

Minor fixes (bug, typo, small polish) can be committed directly to the existing iteration branch.

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
