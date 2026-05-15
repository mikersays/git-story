# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file HTML slide deck (`docs/index.html`, 16 slides) that teaches Git through a sustained "supreme creature in a lab" analogy. Deployed to GitHub Pages from the `docs/` folder.

No build, no dependencies, no tests. The dev loop is: edit `docs/index.html` → reload the browser.

```
xdg-open docs/index.html        # Linux preview
```

## The analogy is load-bearing

Every slide reuses one metaphor. Future edits must preserve it or the deck loses coherence:

- **Supreme creature** = the `main` branch / canonical repository (source of truth)
- **Lab** = the repository
- **Clone** = a *branch* (this overloads Git's `git clone`; the deck currently doesn't disambiguate — see "Known gaps")
- **Fruit** = a commit
- **Side effects** = bugs
- **Scientist** = developer
- **Merging clones back into the supreme** = `git merge` / pull request

## Architecture of the single file

`docs/index.html` is structured top-to-bottom as: `<head>` (palette CSS variables, all styles, motion keyframes, breakpoints) → 16 `<div class="slide">` blocks → `<script>` (keyboard nav + IntersectionObserver).

### Slide structure

Every slide is `<div class="slide">` (or `slide title` for slide 1, `slide closing` for slide 16), full-viewport, with CSS `scroll-snap-align: start`. Each contains:

1. An inline SVG illustration (`class="illust illust-icon-<name>"` or `illust-hero-<name>`)
2. An `<h2>` (or `<h1>` on the title slide)
3. Prose / lists / code blocks
4. `<div class="slide-number">N / 16</div>`

When adding or removing a slide, the slide-number denominators in **all** slides must be updated together.

### Motion system

Two layers:

1. **Per-illustration loops** — `bob`, `pulse-glow`, `sway`, `shimmer` keyframes applied to specific `.illust-*` classes. Negative `animation-delay` is used to desync neighboring icons (e.g., `.illust-icon-clones { animation-delay: -0.6s; }`).

2. **Entrance stagger** — the script adds class `in-view` to each slide as it scrolls past `intersectionRatio >= 0.5`. CSS then animates `h2`, `p`, `ul/ol`, `pre` with a `fade-up` keyframe and staggered delays (`0.05s`, `0.15s`, `0.25s`, etc.). Initial state (opacity 0) is gated by a `.js` class added to `<html>` in a head-inline script — without that gate, no-JS users would see a blank deck.

`prefers-reduced-motion` zeroes all animations and restores opacity. Don't introduce motion that bypasses this guard.

### Responsive breakpoints

| Width | Behavior |
|---|---|
| `≥ 1400px` | Content capped at 1200px centered column. Slide padding becomes `max(12vw, (100vw - 1200px) / 2)`. Slide-number `right` uses the same formula so the pill stays at the content's right edge. |
| Default desktop | Padding `8vh 12vw`. |
| `≤ 768px` | Slide padding tightens, content allowed to scroll inside the slide (`overflow-y: auto`). `100dvh` fallback so iOS Safari URL bar doesn't crop. |
| `max-height: 480px and landscape` | Switches font sizing from `vw`-scaled to `vh`-scaled. |

Icons live in normal document flow directly above each slide's heading at every viewport (~84px on desktop, ~70px on mobile, ~60px on small phones). Heroes on the title and closing slides are centered above their heading. Only the slide-number pill is absolutely positioned (bottom-right corner of the content column).

### SVG illustrations

15 inline SVGs use **hardcoded hex values**, not CSS variables. This is intentional: `var(--mint)` in an SVG `fill="..."` presentation attribute does not work cross-browser. If the palette changes, the SVG fills must be updated by hand. The palette-to-hex mapping lives implicitly in `:root` at the top of `<style>`:

```
--mint    #86efac    creature
--peach   #fdba74    fruit
--lavender#c4b5fd    clones
--coral   #fb7185    rotten / warning
--honey   #fcd34d    sparkles, approval
--rose    #f0abfc    playful warmth
--sky     #7dd3fc    glass / liquid
```

## Known gaps (intentionally unaddressed)

The deck was reviewed by four Playwright-driven assessor agents. These findings were flagged but not fixed — keep in mind if reworking content:

- **"Clone" word collision**: slide 3 uses `git clone <url>` (copy a whole repo) and slide 4 calls a *branch* a "clone." The deck doesn't disambiguate.
- **PR vs. local merge**: slide 8 ("The Merge Ritual") frames `git merge` and pull requests as the same thing. Slide 15 partially compensates with `gh pr create` / `gh pr merge`.
- **Missing concepts**: the staging area as a *selection* step (not just ceremony), `.gitignore`, `git diff`, HEAD, remote auth.
- **Accessibility**: no `<main>` / `<section>` landmarks, no skip link, no visible focus ring on focusable elements (the `<pre>` blocks become focusable via auto scroll-container focus). Color contrast does pass AA throughout.

## Deploy

```
git push                        # master:/docs is the Pages source
```

Pages source is configured in repo settings (Branch: `master`, Folder: `/docs`). Live URL: `https://mikersays.github.io/git-story/`.

## Tooling notes

- The `.playwright-mcp/` directory and `slide*.png` files in the repo root are artifacts from past Playwright-driven assessor agents; both are gitignored.
- Subagents have been used heavily for parallel work (narrative writers, SVG illustrators, motion designers, Playwright assessors). Each returns a snippet; the main agent assembles. If you spawn assessors that drive Playwright, give them an absolute `file://` path to `docs/index.html`.
