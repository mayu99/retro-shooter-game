# Retro Shooter Game

A retro-style top-down browser shooter — no dependencies, no build step. Open `shooter.html` and play.

## How to Play

| Input | Action |
|-------|--------|
| Arrow Keys / WASD | Move |
| Mouse | Aim gun |
| Left Click (hold) | Shoot |
| ESC | Pause |
| Enter | Confirm / Start |

## Features

- 5 levels with 2–5 waves each
- 3 enemy types: Grunt, Runner, Tank
- Pixel-art sprites drawn with HTML5 Canvas (no image files)
- Walk animations, hit flash, death particle bursts
- Tanks shoot back on Level 4+
- HP heal between levels
- High score tracking (session)
- CRT scanline overlay for that retro feel

## Running

```
python -m http.server 8080
```
Then open `http://localhost:8080/shooter.html`

Or just open `shooter.html` directly in your browser (works as a local file).
