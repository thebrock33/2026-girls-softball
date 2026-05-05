# SBMSA 2026 Girls Softball — Spring Season Site

A single-file fan dashboard for the **Spring 2026 SBMSA Girls Softball** season, covering all three age divisions. Data sourced from [sbmsa.net](https://sbmsa.net/schedules).

---

## Age Divisions

| Age | Divisions | Teams | Playoff Brackets |
|-----|-----------|-------|-----------------|
| **6U** | Alo · Finch · Chamberlain | 37 | 🥇 Gold (DE) · 🥈 Silver · 🥉 Bronze · 🟤 Copper |
| **7U** | Abbott · Fernandez | 22 | 🥇 Gold (DE) · 🥈 Silver · 🥉 Bronze |
| **8U** | Finch · Osterman | 22 | 🥇 Gold (DE) · 🥈 Silver · 🥉 Bronze |

---

## Features

### Standings
- Combined view ranks all teams across divisions by winning percentage
- Per-division views show full standings with W/L/T, GB, PCT, current streak, and coach
- Colored row accents indicate each team's playoff bracket placement
- Playoff probability bar shows each team's projected bracket destination

### Schedules
- Expandable per-division schedule (click **Show Schedule** under any standings table)
- Completed games show final scores with win/loss color coding
- Upcoming games show date, time, and a map link to the field location
- Click any team name in the standings to open a **team card modal** with their full game log and upcoming schedule

### Playoff Brackets
- SVG bracket diagrams with connector lines for each round
- Date, time, and field location displayed under each matchup
- **Projected outcomes** — a Poisson Monte Carlo model (2,000 simulations) uses each team's regular-season runs-scored and runs-allowed averages to project a score and win probability (e.g. `↳ Proj: Aggies 11–8 (64%)`) for every matchup where both teams are known
- Resolved slots update automatically as results are entered — projected outcomes disappear once a game has a real result
- Eliminated teams are shown struck-through in the seedings legend

### Season Simulator *(7U and 8U)*
- Pick winners for all remaining regular-season games and watch standings update live
- Auto-pick favorites based on current records
- Progress bar tracks how many picks have been made

---

## Source Files

| File | Description |
|------|-------------|
| `softball-2026.html` | The complete site — a single self-contained HTML file |
| `6u-index.html` | Original 6U source page |
| `7u-index.html` | Original 7U source page |
| `8u-index.html` | Original 8U source page |

---

## Updating the Data

All data lives in JavaScript objects at the top of the `<script>` block inside `softball-2026.html`. Open the file in any text editor and search for the section you want to update.

### Regular-season scores

Find the `DATA` object for the relevant age and division. Each game entry looks like:

```js
{ date:"Sat 4/25", time:"2:00 PM", home:"Aggies", hs:14, away:"Storm", as:7,
  loc:"SWMS Field 1", url:"https://..." }
```

Update `hs` (home score) and `as` (away score) with the final result. Leave them as `null` for unplayed games.

### Standings

Standings are stored separately from the schedule and need to be updated to match results:

```js
{ place:1, name:"Aggies", w:10, l:0, t:0, gp:10, gb:"--", pct:1.000,
  streak:"Won 10", coach:"Collado" }
```

Fields: `w` = wins, `l` = losses, `t` = ties, `gp` = games played, `gb` = games behind leader, `pct` = win percentage (0–1), `streak` = current streak text.

### Playoff results (6U)

Find `PLAYOFF_RESULTS` near the top of the 6U script block. Add completed games using the game number as the key:

```js
var PLAYOFF_RESULTS = {
  gold: {
    G1: { t1:"Aggies", s1:12, t2:"Comets", s2:8 },
  },
  silver: { ... },
  bronze: { ... },
  copper: { ... },
};
```

`t1` / `t2` must exactly match the team names in `PLAYOFF_SEEDS`. The higher score determines the winner automatically — the bracket advances the team and marks the loser as eliminated.

### Playoff results (7U / 8U)

Same structure — find `PLAYOFF_RESULTS` (or `BRACKET_RESULTS`) in the 7U or 8U script block and add game results the same way.

### Playoff seeds

`PLAYOFF_SEEDS` maps each bracket tier to an ordered list of seeded teams. Update seeds only if the seeding changes before playoffs begin:

```js
var PLAYOFF_SEEDS = {
  gold: [
    { seed:1, name:"Aggies",    div:"alo", w:10, l:0, t:0, pct:1.000 },
    { seed:2, name:"Comets",    div:"alo", w:8,  l:2, t:0, pct:.800  },
    ...
  ],
  ...
};
```

### Playoff schedule

`PLAYOFF_SCHEDULES` holds the date, time, and location for each playoff game:

```js
var PLAYOFF_SCHEDULES = {
  gold: {
    G1: { date:"Tue 5/5", time:"5:45 PM", loc:"SWMS Field 3",
          url:"http://maps.google.com/maps?li=rwp&q=9810+Neuens+Rd" },
    ...
  },
};
```

---

## Technical Notes

- **Single file, no dependencies** — the entire site is one `.html` file. It requires an internet connection only to load the Google Fonts stylesheet; all logic and data are inline.
- **Lazy initialization** — the 7U and 8U scripts don't execute until their tab is first clicked, keeping initial load fast.
- **Projected outcomes** use a Poisson model: for each team, runs-per-game (RPG) and runs-allowed-per-game (RAPG) are computed from the regular-season schedule data. Expected runs for a matchup are `(RPG_A + RAPG_B) / 2` for team A and vice versa. 2,000 simulated games determine win probability and projected score. Results are cached after the first calculation.
- The site is **read-only** — there is no backend. All updates require editing the HTML file directly and re-deploying or re-sharing it.

---

## Data Sources

- Regular-season schedules and scores: [sbmsa.net/schedules](https://sbmsa.net/schedules)
  - [6U Alo](https://sbmsa.net/sites/sbmsa/schedule/674695/6U-Alo) · [6U Finch](https://sbmsa.net/sites/sbmsa/schedule/674696/6U-Finch) · [6U Chamberlain](https://sbmsa.net/sites/sbmsa/schedule/674697/6U-Chamberlain)
  - [7U Abbott](https://sbmsa.net/sites/sbmsa/schedule/674701/7U-Abbott) · [7U Fernandez](https://sbmsa.net/sites/sbmsa/schedule/674702/7U-Fernandez)
  - [8U Finch](https://sbmsa.net/sites/sbmsa/schedule/674707/8U-Finch) · [8U Osterman](https://sbmsa.net/sites/sbmsa/schedule/674706/8U-Osterman)

*Last data update: 6U — May 4, 2026 · 7U — May 5, 2026 · 8U — April 29, 2026*
