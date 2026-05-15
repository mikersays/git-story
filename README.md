# The Supreme Creature — A Story-Driven Guide to Git

A 16-slide HTML deck that teaches Git through a "creature in a lab" analogy. There's only one supreme creature (the `main` branch). Scientists clone it, grow fruit on the clones (commits), and merge their improvements back. Conflicts, rollbacks, pull requests, and the rest of Git's vocabulary all map onto the story.

**Live:** https://mikersays.github.io/git-story/

The deck wasn't written by one person. It was assembled, critiqued, and restyled by roughly a dozen specialized Claude Code subagents running in parallel, then iterated based on their findings. This README is mostly about that process.

---

## The analogy at a glance

| Story | Git |
|---|---|
| Supreme creature | `main` branch (canonical source of truth) |
| Lab | Repository |
| Clone | Branch |
| Fruit | Commit |
| Side effect | Bug |
| Scientist | Developer |
| The merge ritual | `git merge` / pull request |
| Lab notebook | `git log` |
| Rotten fruit | A bad commit (use `git revert`) |
| Central lab | The remote (`origin`) |

---

## How we built it

### Round 1 — Five narrative experts wrote the first draft

The deck was outlined as 15 slides up front. Instead of writing them serially, five subagents were spawned in **parallel**, each owning a contiguous section:

| Subagent | Slides |
|---|---|
| Narrative & analogy expert | 1–6 (story setup, lab, branches, commits, parallel work) |
| Solo workflow expert | 7 (day-to-day rhythm for a lone developer) |
| Team collaboration expert | 8 & 12 (the merge ritual, remote / push / pull) |
| Troubleshooting expert | 9–11 (conflicts, `git log`, revert vs. reset) |
| Best-practices & reference expert | 13–15 (etiquette, cheat sheet, closing) |

Each agent received the **same** analogy mapping and a strict output format — return ready-to-paste `<div class="slide">` blocks, nothing else. The main agent assembled their snippets into a single HTML file.

### Round 2 — Four Playwright assessors critiqued the first cut

Instead of self-evaluating, four assessors were spawned in parallel, each driving their own headless browser via **Playwright MCP**:

- **Visual design** took screenshots at 1440×900 and 1024×768, measured `pre`-block overflow, and caught comment columns being clipped on slides 7, 12, and 14.
- **Educational content** read every slide and flagged that "clone" is overloaded (used for branches in the story, but `git clone` actually means downloading a whole repo), and that slide 8 conflates `git merge` with pull requests.
- **UX & functionality** verified keyboard nav, scroll-snap, console errors, and responsive behavior across four viewports — and noticed a missing favicon.
- **Accessibility** computed WCAG contrast ratios for every text-on-background pair, identified that the slide-number color (`#64748b`) was failing AA, and listed the missing landmarks and focus indicators.

Each returned a concise written report. Their critical findings drove the next round.

### Mobile layer

A direct pass added three responsive breakpoints (`≤ 768px`, `≤ 480px`, landscape-short) with a `100dvh` fallback so iOS Safari's URL bar doesn't crop slides.

### Round 3 — Three creative experts redesigned the look

The original palette was cold slate. After a "make this feel friendly" prompt, three subagents were spawned in parallel — coordinated by pre-defining a **shared palette** (mint creature, peach fruit, lavender clones, coral rotten, honey sparkles, rose warmth) in every agent's prompt. The shared palette was the contract that let their outputs compose without conflict:

- **Friendly aesthetic director** — produced the warm-dark "storybook lab" palette, rounded typography, and a slow sunset-shimmer gradient on the title.
- **SVG illustrator** — produced 15 inline SVG illustrations (two hero illustrations + 13 slide icons): a mint blob creature with rosy cheeks, a single fruit, a stack of clones, a beaker with a creature inside, two creatures merging with a heart, colliding fruits with honey sparks, an open notebook, a rotten fruit with X-eyes, a clipboard, a parchment scroll.
- **Motion designer** — produced CSS keyframes (`bob`, `pulse-glow`, `sway`, `shimmer`, `fade-up`), an IntersectionObserver that adds `in-view` to each slide as it scrolls into view, and a `prefers-reduced-motion` override.

Integration caught a subtle bug: CSS custom properties don't work in SVG presentation attributes, so the illustrator's `fill="var(--mint)"` had to be converted to hardcoded `#86efac` everywhere. This is documented in `CLAUDE.md` so the next session doesn't rediscover it.

### Iterative refinements

After the creative round, smaller adjustments happened directly:

- Keyboard hint on the title slide (`← →` pill keys saying "to navigate"), hidden on mobile
- New slide (15) covering the practical PR workflow — `git push -u`, `gh pr create --fill`, responding to feedback by pushing more commits, `gh pr merge --squash`, then deleting the merged branch
- Content cap at 1200px on wide screens (`max(12vw, (100vw - 1200px) / 2)` padding)
- A bug fix: icons were "floating outside" the capped content area in the 1400–1580px viewport range because the icon's `right` formula didn't match the slide's `padding-right` formula — fixed by anchoring both to the same `max(...)` expression
- Moved icons out of the top-right corner entirely — they now sit in normal document flow above each slide's heading at every viewport. A brief detour through centering was reverted in favor of left-aligned, which reads more like a topic avatar above the title
- Moved the deck to `docs/index.html` for GitHub Pages
- Added `CLAUDE.md` for future Claude Code sessions, and this `README.md`

---

## Coordination patterns that worked

A few things made multi-agent collaboration smooth:

1. **Shared palette as a contract.** The same hex values were pasted into every creative agent's prompt. They couldn't drift.

2. **Strict output format per agent.** Each subagent returned a specific kind of snippet (slide HTML, a CSS rule block, fenced SVG markup) — never "edit the file directly." Assembly always happened on the main thread, which made integration deterministic.

3. **Parallel where independent, sequential where dependent.** Writers and assessors ran in parallel (no shared state). Integration happened serially.

4. **Browser-driving assessors for honest feedback.** A subagent with Playwright MCP can take screenshots, measure layout, run JS in the page, and check contrast ratios. That's much more grounded than asking a text-only agent "does this look good?"

5. **Slice work by output type, not by file region.** One agent does typography, another does illustrations, another does motion. They write to disjoint parts of the CSS, so their outputs concatenate without conflict.

---

## Run locally

```
xdg-open docs/index.html        # Linux
open docs/index.html            # macOS
```

No build, no dependencies. Edit the file, reload the browser.

## Deploy

GitHub Pages serves from `master:/docs`. Push the branch, then in repo settings → Pages, set **Branch: `master`, Folder: `/docs`**.

## Project layout

```
.
├── docs/
│   └── index.html       ← the entire deck (~1100 lines, one file)
├── CLAUDE.md            ← architecture notes for future Claude Code sessions
├── README.md            ← you are here
└── .gitignore
```

---

## Credits

Directed by [@mikersays](https://github.com/mikersays). Built collaboratively with Claude Code's multi-agent orchestration — five narrative writers, four Playwright assessors, and three creative experts (aesthetic, illustration, motion), plus iterative refinements on the main thread.
