# SBMSA 2026 Girls Softball — Spring Season Site

A single-file fan dashboard for the **Spring 2026 SBMSA Girls Softball** season, covering all three age divisions. Data sourced from [sbmsa.net](https://sbmsa.net/schedules).

---

## Age Divisions

| Age | Divisions | Teams | Playoff Brackets |
|-----|-----------|-------|-----------------|
| **6U** | Alo · Finch · Chamberlain | 37 | 🥇 Gold (DE9) · 🥈 Silver (SE9) · 🥉 Bronze (SE9) · 🟤 Copper (SE10) |
| **7U** | Abbott · Fernandez | 22 | 🥇 Gold (DE6) · 🥈 Silver (SE8) · 🥉 Bronze (SE8) |
| **8U** | Finch · Osterman | 22 | 🥇 Gold (DE6) · 🥈 Silver (SE8) · 🥉 Bronze (SE8) |

---

## Navigation

The site has four tabs at the top:

| Tab | Description |
|-----|-------------|
| **SBMSA SPRING 2026** | Summary — bracket champions, upcoming playoffs, playoff results |
| **6U** | Full 6U standings, schedules, and playoff brackets |
| **7U** | Full 7U standings, schedules, season simulator, and playoff brackets |
| **8U** | Full 8U standings, schedules, season simulator, and playoff brackets |

The site opens to the **SBMSA SPRING 2026** summary tab by default.

---

## Features

### Summary Tab (SBMSA SPRING 2026)
- **🏆 Bracket Champions** — shows each bracket's champion once determined, or the number of teams still competing across all ages
- **📅 Upcoming Playoffs** — games scheduled within the next 2 days, grouped by date, with resolved team names, time, and field location
- **🏆 Playoff Results** — all completed playoff games across all ages, grouped by age (6U → 7U → 8U), most recent first

### Standings
- Combined view ranks all teams by PCT with full tiebreaking (head-to-head → best win → worst loss)
- Per-division views with W/L/T, GB, PCT, streak, coach, and playoff probability bar
- **Playoff %** column — multi-segment bar showing gold/silver/bronze/copper probabilities from a 2,000-run Monte Carlo simulation
- Click any team name in standings **or the bracket** to open a team card modal

### Team Card Modal
- Full game log with scores, opponent records, and result coloring
- Upcoming games with projected scores, win/loss outlook, and field location links
- **Playoffs section** at the bottom with completed playoff results
- Bracket label, coach, and division shown in meta row

### Playoff Brackets
- SVG bracket diagrams for all tiers (DE9, DE6, SE8, SE9, SE10)
- Completed game scores shown in bracket slots; winner highlighted
- **Projected outcomes** (Poisson Monte Carlo, 2,000 sims) — disappear once a real result is entered
- Eliminated teams struck through in the seedings legend
- Click any team slot to open their team card modal

### Season Simulator *(7U and 8U only — 6U regular season is complete)*
- Pick winners for remaining games; standings and bracket probabilities update live
- Auto-pick favorites, reset picks, and track progress

---

## Output File

| File | Description |
|------|-------------|
| `index.html` | Complete site — single self-contained HTML file (~420KB) |
| `README.md` | This file |

---

## Updating the Data

All data lives in JavaScript inside `index.html`. Open in any text editor and search for the section to update.

### Regular-season scores

Find `DATA` for the relevant age/division. Each game entry:

```js
{ date:"Sat 4/25", time:"2:00 PM", home:"Aggies", hs:14, away:"Storm", as:7,
  loc:"SWMS Field 1", url:"https://..." }
```

Set `hs` / `as` to final scores. Leave `null` for unplayed games.

### Standings

Each entry in `DATA[div].standings`:

```js
{ place:1, name:"Aggies", w:10, l:0, t:0, gp:10, gb:"--", pct:1.000,
  streak:"Won 10", coach:"Collado" }
```

Update `w`, `l`, `t`, `gp`, `gb`, `pct`, `streak` after each week.

### Playoff results (all ages)

Find `PLAYOFF_RESULTS` in the relevant age's script block and add completed games:

```js
// 6U (uses var):
var PLAYOFF_RESULTS = {
  gold:   { G1: { t1:"Aggies", s1:12, t2:"Comets", s2:8 } },
  silver: { G1: { t1:"Dragons", s1:11, t2:"Angels", s2:17 } },
  bronze: {},
  copper: {},
};

// 7U / 8U (uses const):
const PLAYOFF_RESULTS = {
  gold:   { G1: { t1:"Cyclones", s1:11, t2:"Thunder", s2:10 } },
  silver: {},
  bronze: {},
};
```

Team names must exactly match seeds. Higher score wins — brackets, elimination, projected outcomes, and the summary tab all update automatically.

### Playoff seeds (6U only)

`PLAYOFF_SEEDS` — update only if seeding changes:

```js
var PLAYOFF_SEEDS = {
  gold: [
    { seed:1, name:"Aggies", div:"alo", w:10, l:0, t:0, pct:1.000 },
    ...
  ],
};
```

### Playoff schedule

`PLAYOFF_SCHEDULES` holds date/time/location per game:

```js
G3: { date:"Wed 5/6", time:"6:00 PM", loc:"SWMS Field 2",
      url:"http://maps.google.com/..." }
```

---

## Technical Notes

- **Single file, no dependencies** — ~420–500KB including embedded logo. Requires internet only for Google Fonts.
- **Lazy initialization** — 7U and 8U scripts run only when their tab is first visited (or when the summary tab loads). Initial load runs only 6U and the summary.
- **Playoff probability** — Monte Carlo (2,000 runs) weights win probability by PCT, applies tiebreaker logic to distribute bracket placements. 6U uses explicit `PLAYOFF_SEEDS`; 7U/8U derive bracket from final standings rank.
- **Projected scores** — Poisson model: expected runs `= (RPG_A + RAPG_B) / 2`. 2,000 simulated games yield win probability and projected score, cached after first calculation.
- **Background image** — SBMSA softball logo embedded as base64 PNG (transparent background) at 10% opacity, centered and fixed behind all content.
- **Read-only** — no backend. All updates require editing `index.html` directly.

---

## Data Sources

- [6U Alo](https://sbmsa.net/sites/sbmsa/schedule/674695/6U-Alo) · [6U Finch](https://sbmsa.net/sites/sbmsa/schedule/674696/6U-Finch) · [6U Chamberlain](https://sbmsa.net/sites/sbmsa/schedule/674697/6U-Chamberlain)
- [7U Abbott](https://sbmsa.net/sites/sbmsa/schedule/674701/7U-Abbott) · [7U Fernandez](https://sbmsa.net/sites/sbmsa/schedule/674702/7U-Fernandez)
- [8U Finch](https://sbmsa.net/sites/sbmsa/schedule/674707/8U-Finch) · [8U Osterman](https://sbmsa.net/sites/sbmsa/schedule/674706/8U-Osterman)

*Last data update: 6U — May 4, 2026 · 7U — May 5, 2026 · 8U — April 29, 2026*
