# Modernization notes (2026-09-22)

Original: https://github.com/TroJanzHEX/Unlimited-Filter-Bot (last commit Dec 2021)
This copy was updated to run on current libraries.

## What changed

- **pyrogram 1.x -> 2.x**: biggest change. v2 uses enums instead of strings:
  - `chat.type == "private"` -> `enums.ChatType.PRIVATE` (same for group/supergroup)
  - `status == "administrator"/"creator"` -> `enums.ChatMemberStatus.ADMINISTRATOR/OWNER`
  - `parse_mode="md"/"html"` -> `enums.ParseMode.MARKDOWN/HTML`
- **Removed `.html` on message text/caption** (pyrogram v2 dropped it).
  Filters now store plain text instead of HTML-formatted text.
  Saved filters still work; only rich formatting (bold/links) in *saved*
  filter content becomes plain text.
- **pymongo 4.x**: `collection.count()` (removed) -> `count_documents({})`.
- **Removed dead deps**: `umongo` (never used in code), `heroku3` (now optional;
  bot runs without it; /status just skips the Heroku quota section).
- **requirements.txt**: pinned modern versions; added `requests` (was imported
  but never declared).
- **runtime.txt**: python-3.9.1 -> python-3.11.9.
- **bot.py**: removed hardcoded `Config.AUTH_USERS.add(str(680815375))`
  (the original author's ID — do NOT ship that). Add your own ID via the
  `AUTH_USERS` env var instead. Session renamed to `filter_bot`, workers 300 -> 100.
- **connections.py**: `get_chat_member(group_id, "me")` -> `get_me()` + id
  (v2-safe).

## Deploy notes

1. Copy `sample_config.py` -> `config.py` (local run) or set env vars (Heroku/VPS).
   You need: `TG_BOT_TOKEN`, `API_ID`, `API_HASH`, `DATABASE_URI`,
   `DATABASE_NAME`, `AUTH_USERS` (your Telegram user ID).
2. `pip install -r requirements.txt`
3. `python bot.py`

Not live-tested against Telegram (needs real credentials) — syntax-checked only.
If pyrogram raises an error on some call, the traceback will point at the exact
line; most likely candidates are in `plugins/filters.py` (`reply_cached_media`).
