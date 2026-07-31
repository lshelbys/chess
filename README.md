# Chess Companion

A warm, low-pressure mobile chess app: play a full legal game against one of three bots
(Wren / Juno / Oakes) or, optionally, against Mistral's API, in one of four game modes, with
captured-piece trays, a move ticker, animated piece movement and a small settings screen (move
hints, coordinates, eight board palettes).

Single self-contained `index.html` — no build step, no dependencies, works offline.

## What it does

- **Complete chess rules** — castling, en passant, promotion to any piece, and draws by
  stalemate, threefold repetition, the fifty-move rule and insufficient material.
- **Play either colour.** Pick White, Black or Either on the home screen; the board flips
  and the bot opens when you take black.
- **Move by dragging or tapping.** Drag a piece to its square, or tap the piece and then
  tap the destination — whichever you prefer. Legal squares light up either way.
- **Pick a piece when a pawn promotes** rather than always getting a queen.
- **Your game is saved.** Close the tab mid-game and the home screen offers to resume it.
  Settings, board colour and your record persist too — all in `localStorage`, nothing leaves
  the device.
- **Real statistics.** Games, win rate, streak and recent results are counted from games you
  actually finish.
- **A nudge when you want one** — the ✦ button runs the engine for you and flashes a
  reasonable move.
- **Take a move back** with ↺, which rewinds past the bot's reply to your own turn.
- **Keep a game you liked.** When a game ends, *Copy game (PGN)* puts the whole game on
  your clipboard in standard PGN — ready to paste into any analysis board. The moves are
  also shown in a selectable box, so it still works where the clipboard is locked down.
- Soft move sounds and haptics, switchable in Settings.
- **Eight board colours** to choose from in Settings.
- **Four game modes**, picked on the home screen before you start:
  - **Classic** — the standard game, no twists.
  - **King of the Hill** — the four centre squares (d4, d5, e4, e5) are marked on the
    board; walk your king onto one and you win immediately, checkmate or not.
  - **3-Check** — land three checks on your opponent (across the whole game, not in a
    row) and you win on the spot, even if their king is otherwise safe.
  - **Speed Chess** — five minutes a side. A live clock sits next to each player; if
    either clock hits zero, that player loses instantly.
- The page's viewport is pinned — pinch-zoom, double-tap-zoom and browser-chrome
  scroll drift are all disabled, so the layout stays put on a phone.
- **Optional Mistral-backed opponent.** Turn on "Play against Mistral" in Settings and paste
  a Mistral API key — moves are then chosen by `mistral-small-latest` instead of the built-in
  engine. The key is saved only in your browser's `localStorage` (a separate key from the rest
  of the app's settings, so it's easy to find and clear); it is never written into `index.html`
  and is sent directly from your browser straight to `api.mistral.ai`, nowhere else. If a
  request fails, times out, or the model returns something that isn't a legal move, the game
  falls back to the on-device engine automatically and says so under the bot's name — a turn
  never stalls waiting on the network.

## Accounts & friends (optional, powered by Firebase)

Accounts are **off by default** and entirely optional — the app plays fully offline without
one. Turn them on by giving it a Firebase project. Once configured, the **Friends** tab
(Play · Friends · You · Settings) lets you create an account, sign in, get a shareable
**username**, send/accept friend requests, and every finished game is saved to your
account (visible under *You*). Sign-in uses email + password; the app talks to Firebase over
its REST API, so `index.html` stays a single self-contained file with no SDK.

**Note on privacy:** local play still leaves nothing on the device's network. Account
features (sign-in, saved games, friends) obviously use the network and store data in your
Firebase project.

### Set it up

1. **console.firebase.google.com → Add project.** The free *Spark* plan is plenty.
2. **Add a Web app** (`</>`), register it, and note the `apiKey` and `projectId` from the
   shown `firebaseConfig`. These are *not* secrets — they are meant to ship in the browser;
   security comes from the rules below.
3. **Build → Authentication → Get started → Sign-in method → enable Email/Password.**
4. **Build → Firestore Database → Create database** (production mode), pick a region.
5. **Firestore → Rules**, paste the contents of [`firestore.rules`](firestore.rules), Publish.
6. **Authentication → Settings → Authorized domains**, add your site's host
   (e.g. `yourname.github.io`).
7. **Turn it on in the app:** open `index.html` in a text editor and replace the two
   placeholders `YOUR_FIREBASE_API_KEY` and `YOUR_FIREBASE_PROJECT_ID` with your values, then
   save. (These values contain no special characters, so a plain find-and-replace is safe.)
   Reload the site and the Friends tab is live.

Until you do step 7 the Friends tab simply invites you to add a config, and everything else
works as before.

## Host on GitHub Pages

1. Create a new repository on GitHub.
2. Upload `index.html` (and this README) to the repository root, or push:

   ```bash
   git init
   git add .
   git commit -m "Chess Companion"
   git branch -M main
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```

3. In the repo: **Settings → Pages**, set *Source* to **Deploy from a branch**,
   branch `main`, folder `/ (root)`, save.
4. After a minute the site is live at `https://<you>.github.io/<repo>/`.

## Notes

- The bot searches 1–3 plies depending on which companion you pick, then extends the search
  through captures so it stops giving pieces away to an obvious recapture. Each bot has a node
  budget so a reply never blocks the phone for long — Oakes, the strongest, answers in well
  under a second.
- Wren plays with a large random offset on purpose: she is meant to be beatable.
- The move list uses full algebraic notation, including disambiguation (`Nbd2`) and
  check/mate marks (`Qh5+`, `Ra8#`).
- **A note on the Mistral option and API keys in general:** because this project is a single
  static HTML file with no server, any key you enter is necessarily usable from that browser —
  there's no way to hide a secret in code that ships to the browser and still have it work
  client-side. Anyone using *your own* copy of the page on *your own* device is fine; this key
  never appears in the file itself or in this repository. If you host this page publicly and
  hand the URL to other people, don't rely on this field for a shared key — each visitor should
  paste their own, or the AI option should stay off.
