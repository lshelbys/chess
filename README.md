# Chess Companion

A warm, low-pressure mobile chess prototype: play a full legal game against one of three bots
(Wren / Juno / Oakes), with captured-piece trays, a move ticker, animated piece movement and a
small settings screen (move hints, coordinates, three board palettes).

Single self-contained `index.html` — no build step, no dependencies, works offline.

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

- Pawns auto-promote to a queen.
- Draws are detected by stalemate only (no 50-move or repetition rule).
- The bot searches 1–3 plies depending on which companion you pick.
