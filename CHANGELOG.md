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
