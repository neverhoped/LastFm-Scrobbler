# Divine Scrobbler

A tiny Python script that scrobbles a list of tracks to your [Last.fm](https://www.last.fm) profile in bulk. No extra packages needed.

## Features

- Reads tracks from a simple CSV file
- Sends 50 scrobbles per request (the Last.fm API maximum)
- Automatically timestamps tracks backwards from the current time, based on duration
- Optional `--total` flag to loop your list up to a set number of scrobbles
- `--dry-run` mode to preview before sending anything
- Authorizes once and remembers your session
- Retries automatically on temporary Last.fm errors

## Requirements

- [Python 3.7+](https://www.python.org/downloads/)
- A Last.fm account

## Quick start

### 1. Get a Last.fm API key

1. Go to <https://www.last.fm/api/account/create> and log in.
2. Fill in the form:
   | Field | What to enter |
   |---|---|
   | Application name | Anything, e.g. `My Scrobbler` |
   | Application description | e.g. `Personal script to scrobble my own listening history` |
   | Callback URL | Leave empty |
   | Application homepage | Leave empty |
3. Tick "I'm not a robot" and click **Submit**.
4. Copy your **API key** and **Shared secret**. Keep the secret private.

### 2. Download the files

Put `scrobbler.py` and your CSV file in the same folder.

### 3. Make your CSV

Create a file called `tracks.csv` with **no header row**, one track per line:

```csv
"Artist","Track","Album","Timestamp","Album Artist","Duration"
```

Only **artist** and **track** are required. Leave the rest as `""` if you don't need them.

Example:

```csv
"Jeff Buckley","Last Goodbye","Grace","","Jeff Buckley","275"
"Radiohead","Weird Fishes","","","","318"
"Daft Punk","Digital Love","","","",""
```

| Field | Notes |
|---|---|
| Artist | Required |
| Track | Required |
| Album | Optional |
| Timestamp | Optional, format `YYYY-MM-DD HH:MM:SS` (your local time) |
| Album Artist | Optional |
| Duration | Optional, in **seconds**. Defaults to 180 (3 minutes) |

If you leave the timestamp empty, the script stacks the tracks backwards from the current time, so the last row in your file becomes the most recent scrobble.

### 4. Preview (optional but recommended)

```bash
python scrobbler.py tracks.csv --dry-run
```

On Mac or Linux you may need `python3` instead of `python`.

### 5. Scrobble

```bash
python scrobbler.py tracks.csv
```

The first time, the script will:

1. Ask for your API key and shared secret.
2. Print a link. Open it, log in and click **Yes, allow access**.
3. Come back to the terminal and press **Enter**.

Your session is saved in `.lastfm_session.json`, so you won't need to do this again.

## Options

| Option | What it does |
|---|---|
| `--dry-run` | Shows what would be scrobbled without sending anything |
| `--total N` | Loops through the CSV until exactly `N` scrobbles are made |

Example: scrobble a list on repeat until you reach 200 scrobbles:

```bash
python scrobbler.py tracks.csv --total 200
```

### Skip the prompts (optional)

Set your keys as environment variables instead of typing them:

```bash
# Mac / Linux
export LASTFM_API_KEY="your_key"
export LASTFM_API_SECRET="your_secret"

# Windows (PowerShell)
$env:LASTFM_API_KEY="your_key"
$env:LASTFM_API_SECRET="your_secret"
```

## Limits to know about

- **14-day rule:** Last.fm's API ignores scrobbles with timestamps older than 14 days. Older tracks will show up as "ignored".
- **Daily limits:** Last.fm doesn't publish an official cap, but very large amounts per day (a few thousand) may get ignored.
- **No future timestamps.**

## Troubleshooting

| Problem | Fix |
|---|---|
| `HTTP error 503` or "Last.fm problem" | Last.fm is having a temporary issue. The script retries on its own. If it keeps failing, wait a few minutes and run it again. |
| `Could not get session` | You didn't click **Yes, allow access** before pressing Enter. Run the script again and use the new link. |
| `Invalid API key` | Check that you copied the key correctly, with no spaces. |
| Many scrobbles "ignored" | Timestamps are older than 14 days, or you hit a daily limit. |
| `python` not found | Try `python3`, or reinstall Python and tick "Add to PATH". |

## Security

- Never share your **shared secret** or the `.lastfm_session.json` file.
- Add `.lastfm_session.json` to your `.gitignore` if you put this in a repository.

## Disclaimer

Use this tool only for scrobbling music you have actually listened to, such as importing history from another app. Last.fm's terms of service don't allow artificially inflating play counts, and accounts that do so can be flagged or have scrobbles removed. This project is not affiliated with Last.fm. You are responsible for how you use it.
