# Fee Wheel (one-folder version)

All 9 files sit in one folder. No subfolders, nothing to install.

| File | What it is |
|---|---|
| `index.html` | the website (wheel, timer, eligible wallets) |
| `tick.mjs` | runs every minute: reads holders, and every 5 min claims fees, picks a winner, pays |
| `state.mjs` | data for the website (`/api/state`) |
| `round.mjs` | full details of a round, for checking fairness (`/api/round/<id>`) |
| `live.mjs` | viewers, emotes, votes, predictions (`/api/live`) |
| `chat.mjs` | anonymous chat (`/api/chat`) |
| `debug.mjs` | setup checker (`/api/debug`) |
| `netlify.toml` | tells Netlify how it fits together |
| `README.md` | this file |

## Put it online (GitHub)
1. On GitHub: open the repo (or make a new one) → **Add file → Upload files**.
2. Select **all 9 files** at once (Ctrl+A in the folder) and drag them in → **Commit changes**.
   Delete any old files/folders from earlier uploads (`public`, `netlify`, `package.json`, `package-lock.json`).
3. Netlify (site linked to this repo) builds by itself. Build command: leave empty.
4. Open `https://<your-site>.netlify.app/api/debug` → every check should say `"ok": true`.

## Settings (Netlify → Site configuration → Environment variables, never on GitHub)
Required: `TOKEN_MINT` (the CA), `RPC_URL` (Helius link), `POT_PRIVATE_KEY` (pot wallet, base58).
Optional: `TICKER`, `EXCLUDE_WALLETS` (comma separated), `DRY_RUN` (`true` = no payouts),
`CLAIM_FEES` (`true`), `MIN_TOKENS` (250000), `MIN_HOLD_MINUTES` (5), `ROUND_MINUTES` (5),
`MIN_POT_SOL` (0.01), `RESERVE_SOL` (0.005), `WEIGHTING` (`equal` or `balance`).

Links on the page (optional, must start with https://): `X_URL`, `TELEGRAM_URL`, `PUMPFUN_URL`
(pump.fun and the chart link are filled in automatically from the CA).
Live chart: found automatically on DexScreener from the CA. If it ever shows the wrong pair, set `CHART_PAIR`
to the pair address from the DexScreener URL (dexscreener.com/solana/<PAIR_ADDRESS>).
Chat moderation (optional): `CHAT_ADMIN_KEY` = any secret password. Then, from the browser console on the site:
`fetch("/api/chat",{method:"POST",body:JSON.stringify({admin:"YOUR_KEY",clear:true})})` clears the chat;
use `{admin:"YOUR_KEY",ban:"<anon id>"}` to mute someone (the id is the color code shown in /api/chat).
After changing a setting: **Deploys → Trigger deploy**.

Demo of the page without any backend: `/?demo` (or `/?demo&fast` for 30-second rounds).

## Your own mascot images (optional)
Upload your images to the repo next to index.html (e.g. `mascot-left.png`, `mascot-right.gif`), then in Netlify set
`MASCOT_LEFT` = `mascot-left.png` and `MASCOT_RIGHT` = `mascot-right.gif` (or full https links) and redeploy.
They replace the dog and frog in the hero banner and on the 3D stage. Transparent PNG/WebP looks best.

## Game settings (optional, Netlify environment variables)
| Setting | Default | What it does |
|---|---|---|
| `VOTE_OPTIONS` | `1,3,5,10` | winner counts people can vote for (max 15). No votes = 1 winner; tie = the lower number |
| `MEGA_EVERY` | `10` | every Nth spin is a MEGA SPIN (`0` = off) |
| `MEGA_WINNERS` | `3` | minimum winners on a mega spin (a higher vote still wins) |
| `BOOST_PER_HOUR` | `0.1` | diamond hands: slice grows +10% per hour held |
| `BOOST_MAX` | `2` | ...up to 2× (set `1` to turn the boost off) |
| `SITE_URL` | – | your site link, used in winner posts |

## Winner announcements (optional)
- Telegram: `TELEGRAM_BOT_TOKEN` (from @BotFather) + `TELEGRAM_CHAT_ID` (your channel/group, add the bot as admin). Posts every spin.
- X: `X_API_KEY`, `X_API_SECRET`, `X_ACCESS_TOKEN`, `X_ACCESS_SECRET` (app with Read+Write). Free X API ≈ 500 posts/month,
  so it only posts mega spins and wins of `X_POST_MIN_SOL` (default 0.5) or more.

## Cost note
The live features (viewer count, emotes, votes) run Netlify functions. Responses are cached so many visitors stay cheap,
but every open tab sends a small "I'm here" ping once a minute. With hundreds of visitors around the clock you may need a paid Netlify plan.
