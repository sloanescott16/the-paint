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
