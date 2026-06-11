# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step. Open directly or serve statically:

```bash
open index.html                  # macOS quick open
python3 -m http.server 8000      # then visit http://localhost:8000
npx serve .
```

## Architecture

Three files, no dependencies, no bundler:

- **`index.html`** — DOM structure: `<canvas id="board">` (300×600px), `<canvas id="next-canvas">` (120×120px), sidebar HUD (`#score`, `#lines`, `#level`), and `#overlay` for pause/game-over states.
- **`style.css`** — Dark/retro theme; flexbox layout; `backdrop-filter` on overlays.
- **`game.js`** — All game logic (~305 lines, `'use strict'`).

### game.js internals

| Concept | Implementation |
|---|---|
| Board state | `ROWS×COLS` matrix; `0` = empty, `1–7` = piece color index |
| Piece object | `{ type, shape[][], x, y }` |
| Rotation | `rotateCW()` — transpose + reverse rows |
| Wall kicks | `tryRotate()` — tries offsets `[0, -1, 1, -2, 2]` |
| Game loop | `requestAnimationFrame`-based; `dropAccum` tracks elapsed ms |
| Drop speed | `Math.max(100, 1000 − (level−1) × 90)` ms per row |
| Line clear | `clearLines()` — splice + unshift on the board array |
| Ghost piece | `ghostY()` — projects piece straight down |
| Scoring | `LINE_SCORES[cleared] * level`; hard drop +2/cell, soft drop +1/row |

Entry point: `init()` → `spawn()` → `requestAnimationFrame(loop)`.

Game over triggers when `spawn()` detects a collision at piece spawn position.

## Key constants (tunable in game.js)

`COLS` (10), `ROWS` (20), `BLOCK` (30px) — if changed, update canvas `width`/`height` in `index.html` to match `COLS×BLOCK` / `ROWS×BLOCK`.
