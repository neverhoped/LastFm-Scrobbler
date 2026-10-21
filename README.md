# Divine Scrobbler

Add many songs to your [Last.fm](https://www.last.fm) profile at once.

You need: a free Last.fm account and [Python](https://www.python.org/downloads/) installed.

## Step 1: Get a Last.fm API key (one time)

1. Go to <https://www.last.fm/api/account/create> and log in.
2. Fill in **Application name** (anything you like) and **Application description** (anything). Leave the other boxes empty.
3. Tick "I'm not a robot" and click **Submit**.
4. You'll see an **API key** and a **Shared secret**. Keep this page open. Never share the secret.

## Step 2: Make your song list

1. Copy `tracks.example.csv` and rename the copy to `tracks.csv`.
2. Open it in Notepad (or any text editor) and replace the songs with yours.

Each line is one song, like this:

```
"Artist","Song title","Album","","Album artist","Length in seconds"
```

Only the **artist** and **song title** are needed. This works too:

```
"Radiohead","Weird Fishes/Arpeggi"
```

## Step 3: Run it

Put `scrobbler.py` and `tracks.csv` in the same folder. Open a terminal in that folder (Windows: type `cmd` in the folder's address bar and press Enter).

Test first (sends nothing):

```
python scrobbler.py tracks.csv --dry-run
```

Then the real thing:

```
python scrobbler.py tracks.csv
```

The first time, it asks for your API key and secret. Paste them in, open the link it shows, click **Yes, allow access**, then press **Enter** in the terminal. You only do this once.

On Mac, use `python3` instead of `python`.

## Good to know

- **Songs older than 14 days are rejected by Last.fm.** Your songs get timestamped as if you just listened, ending now.
- **Repeat a list:** add `--total 100` to repeat your songs until 100 scrobbles are sent.
- **Error 503:** Last.fm is busy. The script retries by itself. Anything that still fails is saved in `failed_scrobbles.csv`: run the script on that file later. Adding `--batch-size 10` can also help.
- **Never share** your shared secret or the `.lastfm_session.json` file.

## Please use fairly

Use this for music you really listened to, like moving your history from another app. Last.fm doesn't allow faking play counts, and your account can be flagged. Not affiliated with Last.fm. Use at your own risk.
