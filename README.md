# Volatility Game MVP — Math Model POC

Minimal sandbox to validate the math for a **path-based cell betting** game.

Rules used here:

- Each round generates a path over `N` cells for `T` steps.
- A bet is placed on **one** cell.
- The bet wins if that cell is **visited at least once** during the path.
- For each cell \(i\), we compute \(P_i = P(\text{hit cell i})\) and set:
  - \(\text{multiplier}_i = \text{RTP} / P_i\)

What’s included:

- Exact per-cell hit probabilities (DP)
- Multipliers targeting 97–98% RTP (per-cell EV)
- A large simulation mode (up to 20,000,000 bets)
- Seed/nonce display for a demo “provably fair style” round

---

## Run locally (recommended)

### Requirements

- Node.js installed (`node -v` should work)

### Start a local static server

From the project folder (`e:\r0mstaMVP`), run:

```bash
npx --yes http-server "e:\r0mstaMVP" -p 8001 -c-1
```

If port `8001` is taken, pick another (e.g. `8002`).

### Open in browser

Open:

- `http://127.0.0.1:8001/volatility_game_model.html`

---

## How to test the math (RTP)

1. Click **Calculate Multipliers**
2. Click **Run Single Round** (sanity check: path draws, visited cells highlight)
3. In **Simulation Rounds**, try:
   - `1000000` (1M) first
   - then `10000000` (10M)
   - then `20000000` (20M)
4. Click **Run Large Simulation**
5. Watch:
   - **Target RTP**
   - **Actual RTP**
   - **Deviation**

Expected: as the number of bets increases, **Actual RTP converges** toward the target.

---

## Troubleshooting

### `EADDRINUSE: address already in use`

The port is already taken. Quick fix: use another port:

```bash
npx --yes http-server "e:\r0mstaMVP" -p 8002 -c-1
```

Or find/kill what’s using a port (example for `8000`):

```bash
netstat -ano | findstr :8000
taskkill /PID <PID> /F
```

### Opened via `file://` and crypto APIs fail

Use the localhost method above. Some browsers restrict `crypto.subtle` outside HTTPS/localhost.

---

## Deploy to Vercel (static)

This can be deployed as a static site on Vercel. Vercel serves over HTTPS, so `crypto.subtle` works.

Simplest approach:

- Keep `volatility_game_model.html` as-is
- Deploy the repo to Vercel
- Open the deployed URL at `/volatility_game_model.html`

If you want it to load at `/` (homepage), rename `volatility_game_model.html` to `index.html` (or add an `index.html` that links to it).

---

## Notes / scope

- The large simulation uses a **fast deterministic PRNG** so 10–20M bet runs are practical.
- The “provably fair” shown in the UI is a **front-end demo**.
  - For production multiplayer, the server should generate and pre-commit `hash(serverSeed)` before bets close, then reveal `serverSeed` after the round for verification.
