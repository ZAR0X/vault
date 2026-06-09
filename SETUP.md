# SETUP — finish in ~12 min

Goal: your phone buzzes with reminders, you reply to log aptitude, and a tracker updates itself — all free, no Mac, no Claude. Three workflows are already in `.github/workflows/` (reminder, tracker, inbox); they all share the same Telegram secrets.

## A. Make the Telegram bot (3 min, phone)
1. Telegram → search **@BotFather** → `/newbot` → pick any name → **copy the TOKEN** (`123456:ABC...`).
2. Open your new bot, send it `hi` (lets it DM you).
3. Browser → `https://api.telegram.org/bot<TOKEN>/getUpdates` → find `"chat":{"id":NUMBER}` → **copy NUMBER** (chat id).

## B. Push this folder to GitHub (4 min, terminal)
```
cd ~/Developer/Learning/InterviewPrep
git init && git add -A && git commit -m "interview prep + reminder/tracker/inbox bot"
gh repo create interview-prep --public --source=. --push
```
No `gh`? Make a repo named **interview-prep** on github.com, then:
```
git remote add origin https://github.com/ZAR0X/interview-prep.git
git branch -M main && git push -u origin main
```
(Public = unlimited free Actions minutes; the token lives in encrypted Secrets, never in code. Private also works.)

## C. Add secrets + variable (3 min, github.com → Settings → Secrets and variables → Actions)
- **Secrets** → New: `TELEGRAM_TOKEN` = your bot token
- **Secrets** → New: `TELEGRAM_CHAT_ID` = your chat id
- **Variables** → New: `LEETCODE_USER` = `zarox`

## D. Test (1 min)
Repo → **Actions** → enable workflows → **DSA Reminder** → **Run workflow** → Telegram message arrives in ~1 min. ✅
Then reply to the bot with `apti 5` — within ~30 min (the inbox poll) it confirms and counts it.

## How you talk back to the bot
- `apti <n>` — log aptitude questions, e.g. `apti 25`. (Counts toward today.)
- `status` — bot replies with your current DSA + aptitude numbers.
- **anything else** — saved to `INBOX.md` with a timestamp, for your next Claude/Gemini session to read. (This is "what I wanted to say when no AI was running.")

## What runs automatically
- **Reminders:** 08:00 (DSA) · 13:00 (aptitude) · 18:00 (rotating: resume/project/review/mock) · 22:00 (DSA) · 23:30 (day-end) IST.
- **Tracker:** nightly 23:50 IST — pulls LeetCode (`zarox`) + folds in aptitude, updates `TRACKER.md` (streak + debt), commits. Tomorrow's messages show real numbers.
- **Inbox:** every 30 min — processes your replies.
- *Today's scheduled reminders are best-effort (cron just registered); locked in from tomorrow. The step-D manual test is your guaranteed first message.*

## Switching to Gemini later (after June 26)
Open this repo in Gemini CLI — it reads `GEMINI.md` → `context/`. Everything carries over. The bot keeps running untouched.
