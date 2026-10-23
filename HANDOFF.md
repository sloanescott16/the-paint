# The Paint v18, season 2026-27 tidy (job 1023-1706-y26)

Branch y26-season, worktree C:\NIGEL\builds\press-box\paint-y26, cut from 6ff9c58 (the commit GitHub Pages serves today;
local main in the-paint was one commit behind at 4cef1c4). The sibling worktrees: paint-hang-fix and paint-hang-publish hold
the hotfix that is already live; paint-opt1011 (branch optimise-1011) holds unmerged image and cache work (combiner logos,
schedule cache in localStorage). That branch was not merged here: it is a separate optimisation, not a season fix.

## Problems found (probed 23 Oct 2026, 17:10 Central, headless, real ESPN feeds)
What already works: every ESPN feed answers (no dead feeds, season 2027 is right, standings, schedule, team stats and
summaries load), live game center updates every 5 s, scores, status, box score, play by play, shot chart and win
probability are all correct for the live MIL v CHI game and the final DAL at HOU game. No script errors.

1. "Playoff race" label on nearly every game. Cause: inRace() counts any team in the top 10 or within 4 games of 10th;
   one game in, that is all 30 teams. Shows on the Scoreboard ribbons, Schedule, Home and the game center header chip,
   and the Race tab says "Root for" on half the league in October.
2. Season tab says the Mavs are 8th in the West while the top bar, Home and Standings say 11th. Cause: snOrder() breaks
   ties alphabetically (DAL sorts early); the rest of the page uses order() (seed, then projection).
3. Season tab "Pace 0-82 over 82 games" after one game. Cause: pace shown from the first game.
4. Race tab: "Watch your back: Jazz sits - behind." Cause: gbTxt() returns "-" for level teams and the line was not worded
   for a tie.
5. Game center box score is cramped on a desktop: at 1440 px only MIN to FT show, STL, BLK, TO, PF and +/- sit behind a
   sideways scroll. Cause: the box column is 1 of 3.4 grid fractions (about 400 px) for a 13 column table.
6. Scoreboard tab shows only "No games live" before tip-off and after the last final on a game night, with no finals
   or tip times. Cause: renderBoard() lists live games only.
7. With a feed down the page says "Nothing on tonight" (Home) and "No games live" (Scoreboard) as if there were no games,
   and the error banner prints the full ESPN address. Cause: a failed scoreboard call leaves S.days[0] empty, which
   reads the same as an empty day.
8. Season tab "Last result" list mixes preseason games (marked PRE) into the recent results once the season has started.
   Cause: snLast() uses every finished game, preseason included.

## What v18 fixed (all eight above)
1. raceOn(): race labels, "Root for" and race chips wait until Dallas has played 20 games. Before that games read
   "Around the league" and the Race tab's tonight list shows the Mavs game only.
2. snOrder() uses the page's own order() for the current season, so the Season tab now says 11th like everywhere else.
3. Pace shows from 10 games played.
4. A level team reads "is level with Dallas".
5. Game center columns are 1.45 / 1.15 / 0.95: the full box score (all 13 columns) shows at 1440 px.
6. Scoreboard with nothing live shows the day's games, upcoming by tip time then finals; an empty day says
   "No games today" with the next tip.
7. A failed scoreboard call sets S.dayErr: Home and Scoreboard say scores are not coming through, and the error
   banners name the feed and status code (errTxt) instead of the full address.
8. Season "Last result" list drops preseason once a regular season game is final.
Version markers: "v18:" code comments at each change and the v18 commit message, the way v17 did. No visible version text.

## How to run and test
Probe (bun, headless Chrome over CDP, profile C:\NIGEL-scratch\1023-1706-y26\profile, speech stubbed):
`bun C:\NIGEL-scratch\1023-1706-y26\probe\probe.js <worktree> <steps.json> <out prefix>`. ESPN calls are fetched live by
bun (ESPN refuses headless Chrome) and saved under feeds\rec. FAIL=<regex> makes matching feeds answer 503. A "now" in the
steps file shifts the page clock. Step files used: tonight.json, nog-v18.json (Thanksgiving, no games), err-v18.json
(FAIL='scoreboard|summary'), ph-v18.json (390 px), fin.json (21 Oct 11:30 PM, finals only).
Screenshots: C:\NIGEL-scratch\1023-1706-y26\shots (base-* are before, v18-* after).

## Results (23 Oct 2026, 17:15 to 17:30 Central)
- Tonight: all tabs load, no script errors, 58 ESPN calls all 200. Live MIL v CHI game center updates (Q4 8:44 to 5:14
  between loads), box score, play by play, shots, win probability correct. Final DAL at HOU 110-128 breakdown correct.
  DAL at IND preview correct.
- No-games day: Home features the next Mavs game, Scoreboard "No games today", Schedule "No NBA games on this day".
- Feed error: banner "(scores, error 503)", Scoreboard and Home say scores are not coming through, game center
  "(game feed, error 503)". No hang.
- Phone 390 px: no sideways scroll on Scoreboard, game center or Home.
- Finals-only night: Scoreboard shows all 11 finals, Mavs first.

## Unsure / not done
- Not tested: a game in overtime, a postponed game, the 20-game switch-over itself (only reached in late November).
- The Season calendar says "All 80" games: ESPN's Mavs schedule lists 80, the two NBA Cup games are not set yet. Left as is.
- Schedule tab still offers the "Playoff race" sort; before 20 games it holds only the Mavs game.
- Unmerged branch optimise-1011 (paint-opt1011) still holds image and cache work; not part of v18.
- HANDOFF.md and PROGRESS.md are new files in a public repo; they hold nothing personal but Nigel may drop them before publishing.
