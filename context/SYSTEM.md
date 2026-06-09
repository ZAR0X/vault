# SYSTEM — how the automation works

All free, cloud-side (GitHub Actions), independent of Ram's Mac and of any AI.

## Files (repo root = InterviewPrep/)
- `TRACKER.md` — the pressure engine; auto-updated nightly + edited in sessions.
- `SCHEDULE.md` — exam-aware 3-phase plan.
- `INBOX.md` — messages Ram texts the bot that aren't a command; AI reads these at session start.
- `status.txt` — one-line live status, composed by tracker, embedded in reminders.
- `state.json` — DSA tracker state (last LeetCode total, streak, debt).
- `apti.json` — aptitude state (date, today, total).
- `tg_offset.txt` — Telegram getUpdates offset (so replies aren't reprocessed).
- `context/` — this portable brain (PROFILE, PLAN, COACHING, SYSTEM, HANDOFF).

## Workflows (.github/workflows/)
- **reminder.yml** — sends Telegram nudges at 08:00 / 13:00 / 18:00 / 22:00 / 23:30 IST. 18:00 rotates focus by weekday. Read-only.
- **tracker.yml** — nightly 23:50 IST: pulls LeetCode count for `vars.LEETCODE_USER` (= zarox), folds in apti, computes streak/debt, updates TRACKER.md + status.txt, commits.
- **inbox.yml** — every 30 min: polls Telegram replies and processes them (below), commits. Shares `concurrency: repo-writer` with tracker so pushes don't collide.

## Telegram reply commands (Ram → bot)
- `apti <n>`  → add n aptitude questions to today (e.g. `apti 25`). Bot confirms.
- `status`    → bot replies with current DSA + apti numbers.
- anything else → appended to `INBOX.md` with an IST timestamp, for the next AI session. Bot confirms it saved.

## Secrets / variables (GitHub repo settings)
- Secret `TELEGRAM_TOKEN`, Secret `TELEGRAM_CHAT_ID` — used by all three workflows.
- Variable `LEETCODE_USER` = `zarox`.

## Robustness / edge cases
- Mac off / lid closed / offline → irrelevant; runs in cloud.
- Phone offline → Telegram queues + delivers on reconnect.
- Reply double-count → prevented: after processing, a confirming `getUpdates?offset=` clears them server-side even if the git push fails.
- Schedule pausing after 60 days idle → tracker's nightly commit keeps the repo active.
- Cron drift 5-15 min is expected and fine.

## Tuning later
- During July exams: lower DSA quota to 2 in tracker.yml; soften reminder text.
- Optional: per-difficulty LeetCode stats; apti streak/debt; weekly summary message.
