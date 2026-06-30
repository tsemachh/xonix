# Xonix — HTML5 Territory-Capture Arcade

A mobile-first, single-file HTML5 remake of the classic **Qix / Xonix** territory-capture
game. Carve up the playfield, wall off territory with your trail, and claim enough of the
board to clear each level — while dodging bouncing sea balls, edge-running **Sparx**, the
erratic **Qix** line-creature, and the occasional **boss**.

Pure vanilla JavaScript + Canvas — **no build step, no dependencies, no frameworks**.
Just open `index.html`.

**▶ Play it live:** https://tsemachh.github.io/xonix/ *(after first deploy)*

Installable as a Progressive Web App — on a phone, use your browser's *Add to Home
Screen* and it runs full-screen and offline like a native app.

## What it borrows from the whole lineage

This is a deliberate "best-of" of the territory-capture genre:

- **Xonix (1984)** — the grid + breadth-first **flood-fill** capture that the engine is built on.
- **Qix (Taito, 1981)** — the roaming **Qix** line-creature, the **Sparx** that patrol your
  borders, and a fast-draw scoring bonus for grabbing lots of area with a short trail.
- **Volfied (Taito, 1989)** — **power-ups**, end-of-run **bosses**, and a hidden picture that
  is gradually revealed as you claim the screen.

## How to play

Fill **75%** of the field (less on boss levels) to advance.

- **Move:** swipe anywhere on the board, use the on-screen D-pad, or arrow keys / WASD.
- You keep gliding in your last direction. Steer **into the water to draw**, and back
  **onto land to seal off** the area. Any region you wall off that has no enemy in it gets
  filled in.
- **Don't** let an enemy touch you or your unfinished trail, and don't cross your own trail.
- **Power-ups** (collected on your land): 🛡 shield, 🐌 slow, ❄ freeze, ⚡ speed, ♥ extra life, 💥 bomb.
- Chain captures quickly for a **combo multiplier**.

## Controls

| Input | Action |
| --- | --- |
| Swipe / D-pad / Arrows / WASD | steer |
| P / Pause button | pause |
| ✕ (top-right) | quit to menu |

Haptic feedback on captures, power-ups and crashes (where the browser supports the
Vibration API — Android; iOS Safari blocks it).

## Features

- Grid territory-capture with real flood-fill region claiming.
- Multiple enemy types: sea balls, frontier-running Sparx, the Qix line-creature, and bosses every 5th level.
- Power-ups, combo scoring, fast-draw bonus, time/lives end-of-level bonuses.
- A procedurally revealed picture behind each level (Volfied-style).
- Synthesized sound (Web Audio), particles, screen shake, haptics.
- Global **leaderboard** (Cloudflare Worker) with automatic local fallback.
- Rotating "Did you know?" arcade-history trivia.
- Fully responsive, mobile-first, installable PWA, offline-capable.

## Tech / standards

- Single static `index.html` (HTML + CSS + JS inline), plus a small PWA layer.
- **PWA:** `manifest.json` + `service-worker.js` (offline-first app-shell cache) + app icons.
- Mobile-first: responsive canvas, `viewport-fit=cover`, swipe + D-pad, fullscreen.
- No external requests at runtime — fully self-contained and offline-capable.

```text
.
├── index.html            # the entire game
├── manifest.json         # PWA manifest
├── service-worker.js     # offline app-shell cache
├── icons/                # app icons (192 / 512 / maskable)
├── leaderboard/          # optional Cloudflare Worker for global scores
├── .github/workflows/    # GitHub Pages deploy
├── LICENSE               # MIT
└── README.md
```

## Develop

There's nothing to build — open `index.html` directly, or serve the folder:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

(A server, rather than `file://`, is needed for the service worker / PWA to register.)

## Deploy

The repo auto-deploys to GitHub Pages on every push to `main` via
`.github/workflows/deploy.yml`. In the repo's **Settings → Pages**, set **Source** to
**GitHub Actions** once.

## Leaderboard

The global board is optional. Deploy the tiny Cloudflare Worker in
[`leaderboard/`](leaderboard/README.md), paste its URL into the `LB_URL` constant in
`index.html`, and redeploy. Until then the game keeps a local leaderboard in your browser.

## Credits & license

[MIT](LICENSE). Independent, non-commercial fan project for educational purposes;
not affiliated with or endorsed by the rights holders of Qix, Xonix, or Volfied.
Built on the same single-file, PWA-first principles as my
[River Raid](https://github.com/tsemachh/river-raid) remake.
