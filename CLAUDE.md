# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Git Workflow

After completing any meaningful unit of work — a new feature, a bug fix, a content change — commit and push immediately. Never let work accumulate uncommitted.

```powershell
Set-Location "C:\Users\HP\Downloads\retro-shooter-game"
git add <file>
git commit -m "short present-tense summary of what changed"
git push
```

Commit message rules: one line, present tense, lowercase, specific (e.g. `add shield power-up to level 3`, not `update stuff`). Include a blank line and a short body if the why is non-obvious.

## Running the Games

Both files are self-contained single-page HTML5 apps — no build step, no dependencies.

**Serve locally (required for `shooter.html` due to ES module quirks in some browsers):**
```
python -m http.server 8080
```
Then open `http://localhost:8080/shooter.html` or `http://localhost:8080/tictactoe.html`.

`tictactoe.html` can also be opened directly as a `file://` URL.

**Stop the server:**
```powershell
Stop-Process -Name python -Force
```

## Repository Contents

| File | Description |
|------|-------------|
| `shooter.html` | Retro top-down shooter — 5 levels, 3 enemy types, particle FX |
| `tictactoe.html` | Tic Tac Toe — 2-player and vs-computer modes with scoreboard |

## shooter.html Architecture

All game code lives inside a single `<script>` block. The logical sections in order:

1. **Config** — canvas size (`W=800, H=600`), `STATES` enum, `LEVELS` array (5 levels × N waves × enemy groups), `EDEFS` (enemy stat definitions)
2. **Input** — `keys{}` map (keydown/up), `mouse{x,y,down}` (mousemove/mousedown/mouseup); canvas coordinates are scaled back from CSS size to logical size
3. **State variables** — `gs` (current game state), `player`, `bullets`, `eBullets`, `enemies`, `parts` (particles), `lvl`/`wave` indices, `spawnQ` (spawn queue), `killed`/`spawnedTotal` counters
4. **Factories** — `mkPlayer()`, `mkEnemy(type)`, `mkBullet(x,y,angle,isEnemy)`, `spawnParts(x,y,col,n,big)`
5. **Update functions** — `update(dt)` dispatches by `gs`; sub-functions: `updSpawnQ`, `updPlayer`, `updEnemies`, `updBullets`, `updParts`, `checkCols`
6. **Render functions** — `render()` dispatches by `gs`; sprite drawing: `drawPlayer()`, `drawEBody(e)` (shared for live enemies and preview)
7. **Game loop** — `requestAnimationFrame` with delta-time capped at 100 ms

**Wave lifecycle:** `startWave()` builds a staggered `spawnQ[]` (shuffled, timed). Each tick, `updSpawnQ` decrements timers and pushes ready enemies into `enemies[]`. Wave clears when `spawnQ.length===0 && enemies.length===0 && killed===spawnedTotal`. Then `nextWave()` → either `WAVE_TRANS` (2 s countdown) or `LEVEL_COMPLETE` (3 s, then `advanceLevel()`).

**Enemy types:** `grunt` (straight charge), `runner` (fast, zigzag offset every 0.45 s), `tank` (slow, armored, shoots `eBullets` on level 4+ every ~2.5–4.5 s).

**Key tuning constants:**
- `p.fRate` — player fire rate (seconds between shots, default `0.16`)
- `p.spd` — player speed in px/s (default `165`)
- `EDEFS[type].spd` — enemy speed multiplier
- Spawn stagger: `i * 0.32 + random * 0.12` seconds per enemy in queue

## tictactoe.html Architecture

Flat script, no game loop needed. State: `board[9]`, `current` ('X'/'O'), `gameOver`, `scores`, `mode` ('pvp'/'ai').

AI uses priority order: win → block → center → corner → random open cell.
