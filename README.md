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
| **SBMSA SPRING 2026** | Summary — bracket champions, regular season champions, upcoming playoffs, playoff results |
| **6U** | Full 6U standings, schedules, and playoff brackets |
| **7U** | Full 7U standings, schedules, season simulator, and playoff brackets |
| **8U** | Full 8U standings, schedules, season simulator, and playoff brackets |

The site opens to the **SBMSA SPRING 2026** summary tab by default.

---

## Features

### Summary Tab (SBMSA SPRING 2026)
- **🏆 Bracket Champions** — shows each bracket's champion once determined; shows two finalists when the championship game is set; otherwise shows the number of teams still competing
- **🥇 Regular Season Champions** — shows the #1 seed per division per age using full tiebreaker rules (win pct → head-to-head → common opponents → best win → worst loss → coin toss); ties are shown with both team names
- **📅 Upcoming Playoffs** — total remaining games per age group, followed by games scheduled within the next 2 days (plus any past unscored games), grouped by date with resolved team names, time, and field location
- **🏆 Playoff Results** — completed playoff games grouped by age (6U → 7U → 8U), most recent first; today's and yesterday's games shown by default, with older games collapsible per age

### Standings
- Combined view ranks all teams by PCT with full tiebreaking (head-to-head → common opponents → best win → worst loss)
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
- **Champion slot** — each bracket has a dedicated champion column to the right of the final game, connected by a line; shows the winner with gold outline/glow once determined, or a dashed TBD slot
- Eliminated teams struck through in the seedings legend
- Click any team slot to open their team card modal

### Season Simulator *(7U and 8U only — 6U regular season is complete)*
- Pick winners for remaining games; standings and bracket probabilities update live
- Auto-pick favorites, reset picks, and track progress

---

## Output File

| File | Description |
|------|-------------|
| `index.html` | Complete site — single self-contained HTML file (~795KB) |
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

**Important:** `t1` must be the team that plays the top slot in the bracket for that game (determined by seeding/bracket structure). Higher score wins. Brackets, elimination, projected outcomes, and the summary tab all update automatically.

### t1/t2 slot assignment by bracket type

For **SE8** brackets (7U/8U silver & bronze):
- G1=S1(t1) v S8(t2), G2=S4(t1) v S5(t2), G3=S2(t1) v S7(t2), G4=S3(t1) v S6(t2)
- G5=WG1(t1) v WG2(t2), G6=WG3(t1) v WG4(t2), G7=WG5(t1) v WG6(t2)

For **SE9** brackets (6U silver & bronze):
- G1=S8(t1) v S9(t2) play-in; G5=S1(t1) v WG1(t2)
- G2=S4(t1) v S5(t2), G3=S2(t1) v S7(t2), G4=S3(t1) v S6(t2)
- G6=WG5(t1) v WG2(t2), G7=WG3(t1) v WG4(t2), G8=WG6(t1) v WG7(t2)

For **DE6** brackets (7U/8U gold):
- G1=S4(t1) v S5(t2), G2=S3(t1) v S6(t2)
- G3=S1(t1) v WG1(t2), G4=S2(t1) v WG2(t2) — WB semis
- G5=LG2(t1) v LG3(t2), G6=LG1(t1) v LG4(t2) — LB R1
- G7=WG3(t1) v WG4(t2) — WB final
- G8=WG5(t1) v WG6(t2) — LB R2
- G9=LG7(t1) v WG8(t2) — LB final
- G10=WG7(t1) v WG9(t2) — Championship

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

## Tiebreaker Rules (Regular Season Champions)

Applied in order when two or more teams share the same win percentage:

1. **Win pct** — already equal; move to next
2. **Head-to-head** — win pct in direct matchups between tied teams
3. **Common opponents** — win pct vs opponents both teams have faced (excluding each other)
4. **Best win** — highest-ranked (lowest place #) opponent beaten
5. **Worst loss** — highest-ranked (lowest place #) opponent lost to
6. **Coin toss** — displayed as 🤝 Tied

---

## Technical Notes

- **Single file, no dependencies** — ~795KB including embedded logo. Requires internet only for Google Fonts.
- **Lazy initialization** — 7U and 8U scripts run only when their tab is first visited (or when the summary tab loads). Initial load runs only 6U and the summary.
- **Uniform bracket rendering** — all 10 brackets use the same dynamic result-lookup pattern (`slotR`, `winnerSlot`, `slotResolved`) and helper functions. All results come from `PLAYOFF_RESULTS`; no scores are hardcoded in rendering.
- **Champion slot** — each bracket appends a dedicated column to the right of the final game. The slot glows gold when the champion is known and shows a dashed TBD outline otherwise.
- **Playoff probability** — Monte Carlo (2,000 runs) weights win probability by PCT, applies tiebreaker logic to distribute bracket placements. 6U uses explicit `PLAYOFF_SEEDS`; 7U/8U derive bracket from final standings rank.
- **Projected scores** — Poisson model: expected runs `= (RPG_A + RAPG_B) / 2`. 2,000 simulated games yield win probability and projected score, cached after first calculation. Projections disappear once a real result is entered.
- **Background image** — SBMSA softball logo embedded as base64 PNG (transparent background) at 10% opacity, centered and fixed behind all content.
- **Read-only** — no backend. All updates require editing `index.html` directly.

---

## Data Sources

- [6U Alo](https://sbmsa.net/sites/sbmsa/schedule/674695/6U-Alo) · [6U Finch](https://sbmsa.net/sites/sbmsa/schedule/674696/6U-Finch) · [6U Chamberlain](https://sbmsa.net/sites/sbmsa/schedule/674697/6U-Chamberlain)
- [7U Abbott](https://sbmsa.net/sites/sbmsa/schedule/674701/7U-Abbott) · [7U Fernandez](https://sbmsa.net/sites/sbmsa/schedule/674702/7U-Fernandez)
- [8U Finch](https://sbmsa.net/sites/sbmsa/schedule/674707/8U-Finch) · [8U Osterman](https://sbmsa.net/sites/sbmsa/schedule/674706/8U-Osterman)

*Last data update: 6U — May 4, 2026 · 7U — May 5, 2026 · 8U — April 29, 2026*
