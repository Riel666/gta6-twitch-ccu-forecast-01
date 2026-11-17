# GTA VI Launch-Day Twitch Forecast — gta6-twitch-ccu-forecast-01

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/Riel666/gta6-twitch-ccu-forecast-01/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/Riel666/gta6-twitch-ccu-forecast-01/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

Forecasts, hour by hour, how many people watch Grand Theft Auto VI live on
Twitch over the 24 UTC hours of its release day (2026-11-19), from everything
recorded up to the end of the day's first hour. For each of hours 2–24 it
writes five quantiles (5%, 25%, 50%, 75%, 95%) of that hour's concurrent
viewer count.

## Run it

```sh
node main.ts
```

Reads every `<id>.in` in `$MAKEFUN_INPUT_DIR` (else `/input`) and writes
`<id>.out` to `$MAKEFUN_OUTPUT_DIR` (else `/output`). Node 22, standard
library only, no network.

## How it works

The shape of a big launch's Twitch day is stable once anchored at the first
hour's print. Every past launch in the case file unlocked at midnight local
time, rolling around the world, so the UTC-day curve is driven by the same
unlock wave: a small hour-1 print means the big markets have not unlocked
yet and the curve climbs steeply; a huge hour-1 print means the launch is
already everywhere and the curve stays flat.

For each hour h = 2..24, the program takes the distribution of
log(viewers_h / viewers_1) across the case's past launches (`comparables`),
weighting each launch by how close its own hour-1 print is to this case's
(a Gaussian kernel, bandwidth 1.0 in log space), and reads the five scored
quantiles off that weighted distribution. Hours with too few launches fall
back to the house baseline's persistence forecast. Nothing is fitted to the
practice answers — the practice launches are not even in the practice cases'
comparables.

Local validation (leave-one-out over the 37 usable comparables in the kit):
mean pinball loss **0.40** vs **0.93** for the persistence baseline
(median 0.25 vs 0.32; on launches with hour-1 ≥ 100k viewers — the regime
GTA VI will be in — 0.22 vs 0.26). Deterministic: same input bytes give
same output bytes. The TypeScript entrypoint was cross-checked against an
independent Python implementation of the same kernel model: identical
quantiles (0.0 relative difference) on all four practice cases.

---

<sub>Entered on [make.fun](https://make.fun/?ref=fdw7289p&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>
