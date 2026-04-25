# FLOP Map — autonomous build log

Session started 2026-04-24. Each feature is its own commit, so you can test and
then keep (leave as-is), revert (`git revert <sha>`), or cherry-pick what you
like before pushing to origin/main.

All commits are **local only** until you push. Check `git log --oneline` to see
what's staged. Live site (kitwebster-art.github.io/compute-atlas) is untouched
unless you `git push`.

---

## How to test a feature

```bash
cd ~/Documents/Work/Compute-Atlas/public
python3 -m http.server 8765   # or use Claude preview server (port 8767)
open http://localhost:8765
```

## How to roll back a single feature

```bash
git revert <commit-sha>     # creates a new commit that undoes it
# OR, if you haven't pushed yet:
git reset --hard <sha-before-feature>    # destructive; nukes later commits too
```

## How to push everything you like

```bash
git push origin main         # deploys to GitHub Pages
```

---

## Features built this session

(each section added as I ship the feature — most recent at top)

---

### `c5cfbbe` · Hide Google Maps link when location is undisclosed

Epoch's CSV uses **country-centroid coordinates** as a placeholder when
the exact site isn't public — e.g. "Meta 100k" and "Oracle OCI Supercluster"
both list lat=39.381266, lng=-97.922211 (the geographic centre of the
contiguous US, in a Kansas field). Clicking "Open in Google Maps" landed
users in random farmland.

Now we only show the Maps link when `location` is specific (not empty,
not just the country name). Confirmed sites like "3231 Riverport Rd,
Memphis, TN" still get a working link — and we now use the address
string as the search query rather than raw coords (more reliable). For
undisclosed sites the link is replaced with a dimmed
"Map link unavailable · location undisclosed" line.

**Revert:** `git revert c5cfbbe`

---

### `3e170f2` · Hover sparkline bars to preview year stats

The growth sparkline (timeline) now updates the caption on hover, not
just click. Hovering a bar shows "{year}: {MW} added · {YoY %}" without
moving the slider; mouse-leave restores the all-time caption. Native
SVG `<title>` tooltips also added per bar for browsers that show them.

**Revert:** `git revert 3e170f2`

---

### `95e7cc4` · Context-aware share blurbs

The X / Reddit / HN share buttons now build their text from the live
filter state. Default view → "FLOP Map: a live 3D map of every known
AI compute cluster on earth..." Filtered to e.g. China + live →
"FLOP Map (China live): 23 clusters · 1.4 GW · 350k H100-eq." The
shared link itself is the current URL (URLSearchParams-based filter
state is already preserved), so recipients land on the same view.

**Revert:** `git revert 95e7cc4`

---

### `231d193` · Click status mix to filter

Both the segments and the legend pills under the masthead status-mix
bar are now clickable. Click "5% live" to instantly drop the view to
the 535 operational clusters; click again to restore. Same for Planned
and Other. One-click access to the most-asked filter on the dashboard.

**Revert:** `git revert 231d193`

---

### `8661785` · Status mix bar (live vs planned vs other)

Under the cluster count and totals strip in the top-right, a small
stacked bar now shows what fraction of the visible total power is
**live (Existing)** vs **planned** vs decommissioned/other, with a
tiny legend showing the percentages. Surfaces the central narrative
of the dataset at a glance: at the global default ≈ 5% of tracked
compute is online, ≈ 95% is planned. Updates live with filters.

**Revert:** `git revert 8661785`

---

### `17e6cc9` · Bake attribution + headline stats into PNG snapshots

The Save PNG button now composites the globe onto a 2D canvas and overlays:

- top-left: gold "FLOP MAP" wordmark + live "{n} clusters · {GW} · {H100-eq}"
  matching the current filtered view
- bottom-right: "flopmap.com · data: Epoch AI (CC BY) · by Kit Webster"

Every shared screenshot now carries the source and the headline stat, so
press / social posts can't lose the attribution.

**Revert:** `git revert 17e6cc9`

---

### `2856602` · Save PNG snapshot of current globe view

New "Save PNG" button in the action row (next to Surprise me / Export CSV).
Captures the globe canvas exactly as currently rendered — view angle, view
mode, filters, all preserved — and downloads it as a 1280×800 PNG with a
date-stamped filename. Useful for press, social posts, decks. Required
flipping Globe.gl's WebGL renderer to preserveDrawingBuffer:true.

**Revert:** `git revert 2856602`

---

### `4015c7d` · Share buttons in About modal

X (Twitter), LinkedIn, Reddit, Hacker News, plus a "Copy link" fallback —
all wired to platform-specific intent URLs with a launch-ready blurb
("FLOP Map: a live 3D map of every known AI compute cluster on earth.
~55 GW · ~79M H100-equivalents · 691 sites. Public data, open source.").
Lives under a new "Share" section in the About modal alongside the
existing embed snippet.

**Revert:** `git revert 4015c7d`

---

### `2a96bf5` · Auto-promote MW totals to GW in breakdowns

The leaderboard, chip-vendor breakdown, and country breakdown all displayed
totals like "8.6k", "14.9k", "31.3k" under an "MW" header — values that
should obviously read as GW. Now any total ≥ 1000 MW renders as GW
(e.g. "8.80 GW", "14.9 GW", "33.0 GW") and the unit header reads
"MW / GW" so you know the column auto-scales. H100-eq mode unchanged.

**Revert:** `git revert 2a96bf5`

---

### `e5807a5` · Tooltips on the totals line

Hover the top-right totals (e.g. "55.2 GW · 79.45M H100-eq · 157.2 ZFLOP/s")
to see glossary tips:

- Power shows "≈ 184M households at avg US consumption" — turns the abstract
  gigawatts into a tangible comparison
- H100-eq explains the normalisation across GPU generations
- FLOP/s explains "peak theoretical, summed"

Makes the stats legible to non-specialists without dedicating real estate
to an explanation.

**Revert:** `git revert e5807a5`

---

### `b189f2c` · Embed mode (`?embed=1`) + iframe snippet

Add `?embed=1` to the URL to hide the masthead, filter sidebar,
leaderboard, timeline, and search — leaving just the globe with a small
legend, a compact footer and a "FLOP MAP → open full" badge top-left that
links back to the full site. Embed mode is chrome-light and iframe-ready.

The About modal now contains the exact `<iframe>` snippet ready for
journalists/bloggers to paste into their CMS, with a one-click Copy
button.

**Test:** open `/?embed=1` on the live site, or click About → Copy in
the embed code section and paste into a Medium / Substack draft.

**Revert:** `git revert b189f2c`

---

### `0e538fa` · About modal

Added an "About" link to the footer (next to "Press ?"). Clicking it
opens a modal with:

- What FLOP Map is and the Epoch AI data behind it (691 clusters,
  ~55 GW, ~79M H100-eq)
- How it works (dots, click-for-specs, filter, pin-to-compare)
- Data caveats (253 anonymised Chinese clusters sharing one centroid,
  missing FLOP/s on planned sites)
- Credits (Kit Webster, Globe.gl, Epoch AI, GitHub)

Closes with Esc or a backdrop click. Gives new visitors landing from
social shares immediate context without cluttering the main UI.

**Revert:** `git revert 0e538fa`

---

### `9f5a8fc` · Mobile polish

Tightened the mobile layout (<800px viewport):

- Compact masthead (smaller heading, tagline hidden)
- Compact counter (hide stats-line, smaller fonts)
- Slide-in filter panel: floating gold "Filters" pill at bottom-left
  toggles the panel open; max-height 50vh with internal scroll
- Region presets wrap onto two rows for small screens
- Footer shrinks and wraps to the right-hand side so it doesn't collide
  with the Filters button
- Timeline and detail panel sized for small screens
- Legend + leaderboard hidden on mobile (press `F` for fullscreen to
  reclaim chrome space)

Now usable on a phone — previously the sidebar panels overlapped the
globe and filters were hard to reach.

**Revert:** `git revert 9f5a8fc`

---

### `c2e0ee3` · Top Countries breakdown + scrollable sidebar

Added a second breakdown panel below "By chip vendor" that lists the top 6
countries by MW (or H100-eq) in the currently filtered set. Each country
row is clickable — tap to filter the entire map to that country, tap again
to clear. United States dominates at ~31 GW, UAE second at ~6.4 GW,
Brazil 4.8 GW, Korea 3.1 GW.

Also made the right-hand sidebar scroll internally when content exceeds
viewport height (which happens with the three new panels stacked).

**Revert:** `git revert c2e0ee3`

---

### `34c7281` · Year-over-year delta in timeline caption

The sparkline caption now shows YoY growth: when you scrub to 2024, it
reads "2024: 1.20 GW added · +176% vs 2023". Quick way to see the
exponential commissioning-pace story without opening a spreadsheet.

**Revert:** `git revert 34c7281`

---

### `a2e24fe` · Chip-vendor breakdown card

New panel under the Top Operators list in the right-hand sidebar. Groups
every visible cluster by chip vendor (NVIDIA, AMD, Google TPU, Huawei,
Intel, AWS, Microsoft, Meta, Other) and shows a gold bar per vendor sized
to its share of the current filtered total. Re-sums live as filters change.

Global default: NVIDIA dominates at ~15k MW vs everyone else combined
under ~700 MW — the scale of their moat, visualised in one glance.

**Revert:** `git revert a2e24fe`

---

### `07a65c1` · Fullscreen toggle (F key)

Press `F` to enter/exit true browser fullscreen. Combine with `H` (press
mode) for a zero-chrome, edge-to-edge globe — perfect for kiosks,
presentations, or recording clean screen captures. `F` added to the help
overlay (`?`).

**Revert:** `git revert 07a65c1`

---

### `2f8537e` · Contextual legend (colour ramp for heatmap / hex views)

The bottom-left legend used to always show the Status dot list — meaningless
when you were in Heatmap or Hex-bin view. Now it's contextual:

- **Points view:** status list (as before, still clickable to filter)
- **Heatmap view:** "POWER DENSITY" (or "H100-EQ DENSITY") colour ramp with
  Sparse → Hotspot labels
- **Hex-bin view:** same ramp with Sparse → Tall-stack labels

Weight-mode change (Power ↔ H100-eq) relabels the ramp live.

**Revert:** `git revert 2f8537e`

---

### `dc89ffe` · Data-freshness stamp in footer

Footer now reads "Made by Kit Webster · Data: Epoch AI · CC BY · refreshed
Apr 2026 · Press ?". The "refreshed …" pill is pulled from the CSV file's
`Last-Modified` HTTP header on load, with a tooltip showing the exact date.
Tells visitors how current the numbers are without you needing to hand-edit.

**Revert:** `git revert dc89ffe`

---

### `f583997` · Share-this-view button + auto-rotate resume

Two small niceties:
- **Share this view** button in the filter sidebar copies the current URL
  (with all filters baked in via URL state) to the clipboard. One-tap share.
- The globe's auto-rotate used to stop forever on first interaction. Now it
  resumes after 45s of idle time, as long as no panel / modal / overlay is
  open — so a kiosk or idle browser tab goes back to gently spinning.

**Revert:** `git revert f583997`

---

### `5b5ca52` · Visible-set totals under the counter

The top-right counter used to say just "691 CLUSTERS SHOWN". Now
there's a gold totals line under it: **55.2 GW · 79.45M H100-eq ·
157.2 ZFLOP/s** (those are the full-world numbers). Filter to a
region, operator, or year and the numbers re-sum live.

**Revert:** `git revert 5b5ca52`

---

### `8be37aa` · Richer hover tooltip

Hover tooltip used to show name, owner, status, MW. Now it also
shows H100-eq, peak FLOP/s, primary chip blend (e.g. "NVIDIA H100 ×
25.0k"), and first-operational year inline with the status pill.
Adds a subtle "Click for details" hint.

**Revert:** `git revert 8be37aa`

---

### `d37dd3f` · Top-5 concentration stat card

Gold card at the top of the operator leaderboard showing what
percentage of visible compute the top 5 operators control, with a
small progress bar. Updates with every filter — filter to Europe to
see how much more concentrated that market is vs the US.

Global default: Top 5 ≈ 57.7% of visible power.

**Revert:** `git revert d37dd3f`

---

### `a2012d0` · Press mode (H key)

Hit `H` to hide every piece of chrome — masthead, filters, legend,
leaderboard, timeline, detail panel, compare tray. Only the globe +
a small "PRESS MODE · PRESS H TO EXIT" pill remains. Perfect for
social posts and journalist screenshots. Hit `H` again to restore.

**Revert:** `git revert a2012d0`

---

### `d9d8fac` · Clickable legend

The status legend in the bottom-left is now a filter shortcut.
Click "Operational" to show only operational clusters; click again
to go back to all. Other rows mute when a filter is active. Stays
in sync with the status dropdown in the filter sidebar (change one,
the other updates).

Also bumped the compare tray to sit above the legend instead of
overlapping it.

**Revert:** `git revert d9d8fac`

---

### `86c2f3e` · Region quick-zoom presets

Six pill buttons under the search bar: **Americas · Europe · MENA ·
East Asia · SEA · Oceania · World**. Clicking one flies the camera
to preset lat/lng/altitude so visitors can drop straight onto the
region they care about.

**Revert:** `git revert 86c2f3e`

---

### `704212e` · Annual MW-added sparkline above timeline slider

Little bar chart above the year slider showing commissioning pace by
first-operational-date year. Bars are clickable to jump the slider. A caption
under the spark reads "All-time 2.88 GW added" when at max year, or "2024:
1.20 GW added" when a specific year is selected.

Data quirk: only clusters with both a `First Operational Date` and a non-null
MW value are counted. Most planned/under-construction hyperscale sites have no
op date yet, so the historical total looks modest vs the top-of-leaderboard
numbers that include future projects.

**Test:** scrub the slider, watch the active bar turn gold and caption update.
Click any bar to snap the slider to that year.

**Revert:** `git revert 704212e`

---

### `1144afb` · Compare pinboard — pin up to 4 clusters, side-by-side modal

New "★ Pin to compare" button inside every cluster's detail panel. Pinned
clusters collect in a tray at bottom-left with an "Open" button that fires a
full-screen side-by-side comparison table. 15 metrics compared — Status,
Country, Operator, Sector, Power, H100-eq, FLOP/s, Total chips, Energy eff.,
Annual MWh, CO₂/yr, Cost (best), Cost / MW, First operational, Chip type.

Best-in-metric highlighted in gold (e.g. highest MW, lowest Cost/MW, lowest
CO₂). Max 4 pins; 5th push drops the oldest.

New keyboard shortcuts: `P` pins the currently-open cluster, `C` opens the
compare modal, `Esc` closes it.

**Test:** open any cluster → click "★ Pin to compare" → pin 2–3 more → click
"Open" in the tray (or press C).

**Revert:** `git revert 1144afb`

---

### `41f159a` · Pulsing gold rings on mega-clusters (≥1 GW)

Globe.gl `ringsData` layer adds an animated halo to every cluster at or above
1 gigawatt. 19 clusters currently qualify (topped by Abu Dhabi UAE/USA 5GW,
Meta $200B Campus Rumor 5GW, DataVolt Neom 1.5GW). Rings update on every
filter/view change.

Purely visual — no data changes.

**Test:** zoom out so several continents are visible; pulses appear over
mega-cluster hotspots (Middle East, US, East Asia).

**Revert:** `git revert 41f159a`

---

### `57e5edb` · Sector filter in sidebar

Extra dropdown under Country. Options are auto-populated from the data
(Private / Public / Public/Private / Academic, etc). Works with URL state
(`?sector=`).

**Revert:** `git revert 57e5edb`

---

### `1745d06` · Surprise-me + CSV export buttons

Two small buttons at the bottom of the filter sidebar:

- **Surprise me** (`S`): opens a random notable operational/construction
  cluster (min 50 MW). Useful for discovery.
- **Export CSV**: downloads the current filtered view as a CSV with 19
  columns (name, owner, status, country, MW, H100-eq, FLOP/s, first op,
  cost, etc). Good for journalists / analysts who want to pull the data.

**Revert:** `git revert 1745d06`

---

### `103390f` · URL state sync (shareable links)

All filters (status, owner, country, sector, min-MW), the view mode, weight
mode, and selected year are now persisted to the URL in query params. Reload
the page and everything is restored. Copy the URL to share a specific
filtered view with anyone.

300ms debounced to avoid flooding the history with micro-updates.

**Revert:** `git revert 103390f`

---

### `920a995` · Keyboard shortcuts + help overlay (`?`)

Press `?` to open a shortcut cheat-sheet overlay. Supported:

| Key | Action |
|-----|--------|
| `/` | Focus search |
| `1 / 2 / 3` | Points / Heatmap / Hex-bin view |
| `R` | Reset globe camera |
| `S` | Surprise me |
| `P` | Pin open cluster |
| `C` | Open comparison |
| `Esc` | Close whatever is on top |
| `?` | This help |

Typing in input fields is safely ignored.

**Revert:** `git revert 920a995`

---

### `8efb92d` · Search bar with type-ahead

Top-of-page search that looks across cluster name, operator, country, and
chip type. Fuzzy-ish scoring: name-starts-with scores highest, then
name-contains, operator, country, chip. Arrow-key navigation, Enter to open.

**Revert:** `git revert 8efb92d`

---

### `22e1044` · Rich drill-down detail panel + brighter heatmap

The single biggest commit of the session. Rewrote the cluster detail panel
from a thin spec list into a magazine-style dossier:

- Headline status pill + context chips (% of global power, homes-powered
  equivalent, rank at launch, noteworthy badge, certainty)
- **Compute & Power** section: MW, annual MWh, H100-eq, peak FLOP/s, total
  chips, energy efficiency, CO₂/yr at grid average
- **Hardware** section: chip blend (primary + secondary types + counts), GPU
  supplier, 8/16/32-bit precision peaks
- **Economics** section: best available cost (reported > inflation-adjusted
  > estimated > hardware), cost per MW, source quote
- **Site & Timeline** section: location, country, coordinates (with Copy
  button), first operational, decommissioned, builds-upon, superseded-by,
  Google Maps link
- **Notes** section: noteworthy flag, data note, quote, expandable "More
  detail" with users / operational date note / certainty note
- Sources list, share-link button
- Heatmap brightness bumped up so small clusters are still visible

Also expanded the CSV row mapping to capture ~30 previously-unused fields
from Epoch's dataset (chip type 2, GPU supplier 2, precision FLOPs, cost
breakdown, certainty metadata, rank at launch, etc).

**Revert:** `git revert 22e1044`

---

### `d53484e` · Point-view bar height scales with size

Dropped the minimum altitude floor on point-view spheres so smaller clusters
aren't visually inflated to match the gigawatts. 10 MW now looks like 10 MW.

**Revert:** `git revert d53484e`

---

### `9ece80c` · Hex-bin bars track aggregate output

Hex-bin height now sums the chosen weight (MW or H100-eq) within each cell
instead of counting points. A hex with one 5GW Phase 2 outranks a hex with
50 small clusters — which matches reality.

**Revert:** `git revert 9ece80c`

---

### `da43f34` · Timeline slider + operator leaderboard

Year slider at the bottom (with Play button that animates through time).
Top-10 operators leaderboard sidebar on the right that reflects the
currently-filtered set.

**Revert:** `git revert da43f34`

---

### Pre-session baseline (already pushed before this session)

- `144b64d` Custom domain: flopmap.com (CNAME file)
- `4535b77` Rebrand Compute Atlas → FLOP Map
- `d874198` "Made by Kit Webster" footer link
- `83728e7` v1: Globe.gl + Epoch CSV + points/heatmap/hex views

---

## Gamma decks

Two decks were sent for generation during this session. Open the URLs below — if generation is still in progress Gamma will show a progress bar; once done it redirects to the finished deck and you can edit / export from there.

1. **FLOP Map — Strategic Overview & Roadmap**
   https://gamma.app/generations/FsYLYchejSt9n2urJPBe3
   Covers: product snapshot, monetisation (API, data subs, sponsored embeds, reports, acquisition), competitors, launch/growth plan, 90-day roadmap, budget, risks.

2. **Kit Webster — Creative + Commercial Directions**
   https://gamma.app/generations/4O59EISI6XjKF2yRqHTj7
   Covers: three overlapping practices (studio / startup / commercial), five strategic plays, 12-month arc, metrics, cash model, three questions to answer.

Both are 16:9 with AI-generated abstract imagery. Edit in Gamma to retheme, add your own screenshots, or export to PDF / PPT.
