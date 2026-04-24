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
