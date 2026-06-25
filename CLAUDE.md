# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step. Open directly:

```bash
open index.html           # macOS
python3 -m http.server 8000  # local server, then http://localhost:8000
```

## Architecture

Single-file logic in `game.js` (~305 lines). No dependencies, no bundler.

**State** — module-level `let` vars: `board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropAccum`, `dropInterval`, `animId`.

**Board** — `ROWS×COLS` matrix; cells store `0` (empty) or color index `1–7` matching `COLORS[]` and `PIECES[]`.

**Game loop** — `requestAnimationFrame`-based `loop(ts)`. Accumulates elapsed ms in `dropAccum`; fires gravity drop when `dropAccum >= dropInterval`. Calls `draw()` every frame.

**Piece lifecycle**: `spawn()` → gravity/input → `lockPiece()` → `merge()` + `clearLines()` → `spawn()`. Game over triggers when `collide()` is true immediately after `spawn()`.

**Rotation** — `rotateCW()` transposes+reverses; `tryRotate()` tries kicks `[0, -1, 1, -2, 2]` columns.

**Rendering** — two canvases: `#board` (300×600) for the play field, `#next-canvas` (120×120) for the preview. `drawBlock()` is the single drawing primitive used by both.

## Tunable constants

`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES` are all at the top of `game.js`. If `COLS`, `ROWS`, or `BLOCK` change, update `width`/`height` on `<canvas id="board">` in `index.html` to match (`COLS×BLOCK` × `ROWS×BLOCK`).
