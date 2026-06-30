# Xonix — Global Leaderboard (Cloudflare Worker)

This folder holds the tiny backend that powers the global **LEADERBOARD**. The game
(a static site) can't store shared data by itself, so scores live in a Cloudflare
Worker + KV store. Cloudflare's free tier is plenty for this.

What gets shipped to players is only the **public Worker URL** — there are no secret
keys in the game. The Worker validates every submission (name length, score range, a
short per-IP cooldown). It is *not* tamper-proof — the browser reports the score — but
that's a fine trade-off for a fun arcade board.

## Deploy it (dashboard, ~5 minutes, no command line)

1. **Create a free Cloudflare account** at https://dash.cloudflare.com/sign-up
   (or log in if you already have one).

2. **Make a KV namespace**
   - Left sidebar → **Storage & Databases** → **KV** → **Create a namespace**.
   - Name it `xonix-scores` → **Add**.

3. **Create the Worker**
   - Left sidebar → **Compute (Workers)** → **Create** → **Create Worker**.
   - Give it a name, e.g. `xonix-scores` → **Deploy** (it deploys a hello-world first).
   - Click **Edit code**, delete everything in the editor, and paste the entire
     contents of [`worker.js`](./worker.js). Click **Deploy**.

4. **Bind the KV namespace to the Worker** (this is the important step)
   - Open the Worker → **Settings** → **Bindings** → **Add** → **KV namespace**.
   - **Variable name:** `SCORES` (must be exactly this, uppercase).
   - **KV namespace:** pick `xonix-scores`.
   - **Save** / **Deploy**.

5. **Grab the public URL.** On the Worker's page it looks like:
   `https://xonix-scores.<your-subdomain>.workers.dev`

6. **Paste it into the game.** Set the `LB_URL` constant near the top of the
   `<script>` in `index.html` to that URL and redeploy. From then on, every player's
   best run shows up on the shared board.

## Test it quickly

```bash
# should return {"scores":[]}
curl https://xonix-scores.<your-subdomain>.workers.dev/scores

# add a test score
curl -X POST https://xonix-scores.<your-subdomain>.workers.dev/scores \
  -H "Content-Type: application/json" \
  -d '{"name":"ACE","score":12345,"level":7}'
```

## How the game uses it

- On **game over**, if your run makes the top 25 you're asked for a name and it's
  `POST`ed to `/scores`.
- The **LEADERBOARD** screen `GET`s `/scores` and shows the worldwide top entries.
- If the URL isn't set yet, or the Worker is unreachable, the game silently falls back
  to a **local** leaderboard stored in your own browser — so the feature always works.

## API

| Method | Path      | Body                                   | Returns |
| ------ | --------- | -------------------------------------- | ------- |
| GET    | `/scores` | —                                      | `{ scores: [ {name, score, level, ts} ] }` |
| POST   | `/scores` | `{ "name": "...", "score": 0, "level": 0 }` | `{ ok: true, rank, scores: [...] }` |
