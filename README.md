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
| `COOLDOWN_SPINS` | `5` | a winner sits out the next 5 spins (taken off the wheel), `0` = off |
| `SITE_URL` | – | your site link, used in winner posts |

## Winner announcements (optional)
- Telegram: `TELEGRAM_BOT_TOKEN` (from @BotFather) + `TELEGRAM_CHAT_ID` (your channel/group, add the bot as admin). Posts every spin.
- X: `X_API_KEY`, `X_API_SECRET`, `X_ACCESS_TOKEN`, `X_ACCESS_SECRET` (app with Read+Write). Free X API ≈ 500 posts/month,
  so it only posts mega spins and wins of `X_POST_MIN_SOL` (default 0.5) or more.

## Cost note
The live features (viewer count, emotes, votes) run Netlify functions. Responses are cached so many visitors stay cheap,
but every open tab sends a small "I'm here" ping once a minute. With hundreds of visitors around the clock you may need a paid Netlify plan.

## Terms screen, music, settings
- First-time visitors see a "Read me first" screen with the terms and must tick 18+ and "I agree" before entering. It's a basic template, not legal advice: have a lawyer look at it if the project grows. If you change the terms, change `TERMS_KEY` (`wof_terms_v1` → `wof_terms_v2`) in index.html so everyone has to agree again.
- Arcade music: 4 original chiptune tracks generated in the browser (no music files). Visitors pick a track, volume or turn it off in ⚙️ Settings.

## THE FEE PIT (3D mode)
The site now opens as a walkable 3D casino ("THE FEE PIT"). The classic page is still there, 100% working, as **FLAT MODE** (button top right; each visitor's choice is remembered). Add `?flat` or `?pit` to a link to force one.
- Desktop: click to walk (WASD, mouse look, Shift sprint, E use, Esc menu), or just click any booth. Phones: drag to look, tap the floor to walk, tap a booth.
- Every station uses the real page: vote booths, CALL IT tanks, jackpot vault, degen table + cooling-down ice, chart lounge (DexScreener), chat booth, wallet teller, hall of winners (Solscan), CA plaque (copy + links), stats plinths. Nothing is faked.
- The 3D wheel uses the real holders: every wallet gets its own slice at its real odds (names + wallet icons where they fit, thin stripes when there are hundreds). At zero the camera flies to the wheel and it lands on the real winner (4.5 s).
- All art, including the FEEBOI mascot, and all sound is generated in code: no image files, no outside images.
- Slow device? It turns bloom off and then drops to a lite mode by itself; FLAT MODE is always one tap away. Settings has a switch for the spin camera.
- Only `index.html` changed for the 3D mode; no new Netlify settings are needed.

## Multiplayer (see other degens in the 3D pit)
- First time in the pit, each visitor is asked "SEE OTHER DEGENS?" (can be changed in ⚙️ Settings).
- Players who join see each other walking around with name tags (their chat nickname), chat bubbles over their heads and emotes. Press **T** (or tap 💬) to say something: it shows over your head for people in your room and also goes into the normal chat. Same rules as the chat: no links, 200 characters, 1 message per 4 seconds.
- It is peer-to-peer: browsers connect directly to each other (up to 15 per room; when a room is full, people go to the next room). Free public relays are only used to find each other. **It costs no Netlify credits.** People in the same room can see each other's IP address; the join prompt and the terms say so.

## Staying on Netlify's FREE plan (important)
New free Netlify accounts get **300 credits per month**. When they run out, Netlify pauses the whole site until the next month. This version is built to use as few credits as possible:
- The spin timer runs **once per 5 minutes** (on the spin mark) instead of every minute. Effective rule: a wallet must be seen at the previous 5-minute mark (so it has held 5 minutes or a bit more).
- Visitors are served from Netlify's cache; the timer clears that cache the moment something changes, so the page is still instant.
- Pages poll slowly and stop completely in background tabs. Multiplayer, bubbles and emotes in the pit go peer-to-peer.

Rough budget per month (1 GB functions, Netlify's credit prices):
- Spin timer: about 60–120 credits (more on rounds with real payouts and fee claims).
- Visitors: about 0.3–0.45 credits per visitor-hour (one person with the page open for one hour).
- **Every deploy costs 15 credits.** Upload all changed files to GitHub in ONE go (one commit), not one by one.
- That leaves room for roughly 350–500 visitor-hours a month (for example 4 people watching 3–4 hours every day).

Check the meter in Netlify → your team → Usage / Billing. If the coin takes off, Netlify Personal ($9, 1,000 credits) or Pro ($20, 3,000 credits) keeps it online; otherwise the site pauses when the 300 credits are gone.
