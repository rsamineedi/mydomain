# Priya's Math Lab (rukminipriya.in)

Personal learning site for the user's daughter Priya (Grade 5). Static single-page site on GitHub Pages, sharing a backend with the sister site `dineshram.in` (Dinesh Ram's Science Corner — see `../dineshram/CLAUDE.md`).

## Stack & hosting
- **Everything is one file**: `index.html` — all CSS and JS inline, no build step, no bundler.
- Hosted on **GitHub Pages**, repo `rsamineedi/mydomain` (note: repo name ≠ domain name — historical).
- Custom domain `rukminipriya.in`, DNS A records on **Hostinger** point to GitHub Pages IPs. `CNAME` file in repo root sets the custom domain.
- `priya-avatar.png` — her photo, used as the circular header avatar (`object-position: center 20%` to frame the face).
- `assets/` — math + geography themed SVGs (globe, compass, triangle, equations, etc.) used as background decoration and activity-card icons.

## Design system
- Fonts: **Fraunces** (serif, headings) + **Inter** (body), loaded from Google Fonts.
- Palette: warm cream/editorial (`--cream`, `--primary` teal-green, `--terracotta`, `--sage`, `--gold`) — a Montra-inspired "Math Lab" aesthetic, deliberately more sophisticated/editorial than a typical kids' site, per explicit user direction.
- CSS custom properties defined once in `:root`; reused throughout.

## Page structure (as of last session)
Header (site title, tagline, avatar) → a fixed **top-right Login button** (shows "Login" or "👤 Name") → **4 centered top-level tabs**: `About Me | Posts | Games | Quiz`, each a `.top-panel` div toggled via `data-toptab` + JS (`activateTopTab`).

- **About Me** (`#aboutPanel`) — currently a draft placeholder card; Priya intends to write this herself later.
- **Posts** (`#postsPanel`) — blog list, currently empty ("No posts yet").
- **Games** (`#gamesPanel`) — **Math Practice** (continuous streak-based arithmetic, not a scored round).
- **Quiz** (`#quizPanel`) — has its **own sub-nav** (`nav.site`, reuses the older `.view`/`.nav-btn`/`showView()` pattern) switching between **Geography** and **General Knowledge**, each a 5-question round pulled from a local question bank (`geoBank`, `gkBank` in the `<script>`).
- **Login** (`#loginPanel`) — reached via the top-right button. Contains the name+PIN login form, and when logged in, a **consolidated "📜 Past Tests"** list (all games, via `GET /api/attempts` with no `game` filter) plus the standard per-quiz "🏆 Scoreboard / 📜 My Trials" widgets inside Geography/GK.

Clicking any past-attempt row (in either the per-quiz widget or the consolidated Past Tests list) opens `#sheetModal` — a **paper/school-answer-sheet-styled popup** (`Patrick Hand` handwriting font, red "graded" stamp, ✓/✗ per question) showing that round's Q&A detail.

## Backend integration
Talks to the **shared** backend at `https://api.dineshram.in` (see `../learning-api/CLAUDE.md`). Auto-detects `localhost`/`127.0.0.1` and uses `http://localhost:3000` instead for local testing.
- `LA_SITE = 'priya'` — this is the `site` value sent to every API call; the backend keys users as `priya:<username>`.
- Any name+PIN combo works — first login with a given name auto-registers it. Multiple people can have separate accounts on this one site.
- `localStorage` keys: `rp_math_best`, `rp_geo_best`, `rp_gk_best` (per-device fallback bests, merged with server on login via `Math.max`), `la_token_priya`, `la_name_priya`.
- Best-score sync and attempt-history recording both go through `laPushScore()` / `laRecordAttempt()` in the script.

## Known gotchas / history
- This repo's git history was **rewritten with `git-filter-repo`** once (2026-08-31) to scrub a `git add -A` that accidentally committed unrelated files, including an internal Rakuten affiliate-program analysis doc. **Never use `git add -A` or `git add .` in this repo** — always add files by name. A `.gitignore` (`.claude/`, `*.md` except `README.md`) is in place as a backstop, but stray `.md` scratch files (`rakuten-affiliate-*.md`, `views.md`) still sit untracked in this directory from unrelated work — leave them alone, they're not part of this project.
- The "Games = Math / Quiz = Geography+GK" split, the "About Me" content being author-pending, and the top-right Login button placement were all explicit user decisions — don't restructure the nav without checking first.
- Question bank length is 5 per round (`len: 5` in the `geoQuiz`/`gkQuiz` config) — was 10 originally, changed on request.

## Testing locally
No build step. Serve with any static server, e.g.:
```
python3 -m http.server 8082
```
Then open `http://localhost:8082/`. The backend auto-switches to `http://localhost:3000` when running on localhost — start `../learning-api` locally too if testing login/sync end-to-end (see that repo's CLAUDE.md).

## Deploying
```
git add index.html   # (or specific changed files — never -A)
git commit -m "..."
git push
```
GitHub Pages rebuilds automatically (~30-60s). Check `gh api repos/rsamineedi/mydomain/pages/builds/latest` for build status if changes don't seem to appear.
