# NapIQ

An API-driven nap tracker that learns what kind of nap actually makes you productive.

## The problem

Naps are hit or miss. Sometimes 20 minutes leaves you sharp, sometimes it leaves you groggier than before you laid down. NapIQ logs every nap you take along with how you felt and how productive your evening was, so over time you can see what actually works for you instead of guessing.

## How it works

1. **Start a nap** via an iPhone Shortcut, which hits the API and starts tracking.
2. **Wake check**: rate how you feel (1-5) and how quickly you fell asleep (fast / slow / not really). This also ends the nap.
3. **Evening check**: a scheduled prompt asks how productive you were (1-5).
4. **Dashboard**: a simple web page charts nap length against productivity, so patterns become visible.

> Nap length comes from manual taps, so results are approximate. See [Limitations](#limitations).

## Stack

- **Backend**: FastAPI + SQLite
- **Client**: iPhone Shortcuts (no app required)
- **Dashboard**: HTML + Chart.js

## API endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/naps/start` | Start a new nap session |
| POST | `/naps/{id}/wake` | Log wake rating and sleep onset speed, and end the nap |
| POST | `/naps/{id}/productivity` | Log evening productivity rating |
| GET | `/naps` | List all nap sessions |
| GET | `/naps/stats` | Aggregated stats (e.g. avg productivity by nap length) |

## Limitations

Nap start and end times are logged by manual taps, so nap length is really "time in bed", not measured sleep. Known sources of error:

- **Falling asleep takes time.** The clock starts when you tap, not when you drift off. The sleep onset rating (`fast`, `slow`, `not_really`) only partly captures this.
- **Forgotten or late taps.** Forgetting to tap wake inflates a nap's duration, and tapping late pads it. A forgotten start tap means the nap is never logged.
- **Noisy inputs overall.** Ratings are self-reported, and other factors (sleep the night before, time of day, evening workload) are not controlled for.

How v1 handles this:

- Planned: naps with no wake log, or with an implausibly long duration, will be flagged as incomplete and excluded from stats rather than guessed.
- Results should be read as hints about patterns, not precise measurements.

## Status

🚧 In progress.

- [ ] Core API endpoints
- [ ] Shortcuts integration
- [ ] Dashboard

## Roadmap

- [ ] Nap length recommendation based on historical data
- [ ] Richer self-reported signals (e.g. filtering by sleep onset speed)
- [ ] HealthKit integration for nightly sleep context
- [ ] Possible future exploration: hardware-based nap detection (motion sensing) to reduce reliance on manual start/stop taps

## Why I built this

I noticed that how I nap after school directly affects how much work I get done that evening, but I had no real data on the pattern, just a vague sense of it. This is an attempt to actually measure it.
