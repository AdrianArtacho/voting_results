# SHOWING — Live Voting Visualization

This repository contains a **static, full-screen visualization** for live audience voting.
It is designed to work together with **SHOWING** (the smartphone interface) and a **public Google Spreadsheet published as CSV**.

The visualization runs entirely on **GitHub Pages**:

* no server
* no database
* no authentication
* just a browser, a CSV, and time

---

## What it does

* Continuously fetches a **public CSV** containing vote events
* Aggregates votes by option
* Displays results as **smooth, animated bars**
* Supports a **counting window** (open / close)
* Shows a **countdown overlay** (“Voting closes in 12s”)
* **Freezes visually** when the voting window closes

Votes that arrive **outside the window** remain in the spreadsheet but are **ignored by the visualization**.

---

## Expected CSV format

The CSV must contain an **event log** with at least two columns:

| Column      | Meaning                                                     |
| ----------- | ----------------------------------------------------------- |
| Option      | The selected option (e.g. “Option A”)                       |
| tsClientIso | ISO timestamp of the vote (e.g. `2026-01-04T11:40:32.599Z`) |

Example:

```csv
Option,tsClientIso
Option A,2026-01-04T11:40:32.599Z
Option B,2026-01-04T11:40:52.950Z
Option C,2026-01-04T11:41:41.202Z
```

> ✅ **Strongly recommended:** use ISO timestamps with `Z` (UTC) to avoid timezone ambiguity.

---

## Basic usage

The visualization is controlled entirely via **URL parameters**.

### Minimal example (count all votes)

```
https://YOURNAME.github.io/SHOWING-viz/
?csv=ENCODED_CSV_URL
```

Defaults:

* option column = 1
* timestamp column = 2
* refresh every 2 seconds

---

## URL parameters

### Required

| Parameter | Description                |
| --------- | -------------------------- |
| `csv`     | URL-encoded public CSV URL |

---

### Data mapping

| Parameter | Default | Meaning                                |
| --------- | ------- | -------------------------------------- |
| `col`     | `1`     | Column index (1-based) for vote option |
| `tscol`   | `2`     | Column index (1-based) for timestamp   |

Example:

```
&col=1&tscol=2
```

---

### Refresh rate

| Parameter | Default | Meaning                       |
| --------- | ------- | ----------------------------- |
| `refresh` | `2`     | Seconds between CSV refreshes |

Example:

```
&refresh=1.5
```

---

### Title

| Parameter | Meaning                |
| --------- | ---------------------- |
| `title`   | Title shown at the top |

Example:

```
&title=CAP%20Voting
```

---

## Voting window (open / close)

The visualization can **count votes only within a specific time window**.

This is independent of when people actually vote.

---

### Option A — Start now, close after N seconds (recommended for live use)

```
&open=now&window=60
```

Behavior:

* counting starts when the page loads
* voting closes automatically after 60 seconds
* late votes are ignored visually but remain in the CSV

---

### Option B — Fixed open and close times (absolute)

```
&open=2026-01-04T11:40:00Z
&close=2026-01-04T11:41:00Z
```

Behavior:

* only votes whose timestamps fall inside this range are counted
* works reliably across devices and timezones

---

## Countdown overlay

When a close time exists, the visualization shows a **floating overlay**:

* before opening:
  `Voting opens in 12s`
* during voting:
  `Voting closes in 12s`
* after closing:
  `Voting closed`

The overlay updates smoothly and independently of the refresh cycle.

---

## Freeze behavior (window closed)

When the voting window closes:

* bars **stop updating**
* values **dim slightly**
* a subtle **freeze animation** is triggered
* the visualization becomes visually “locked”

This makes the end of voting **clearly legible on stage**, without abrupt jumps.

---

## Typical live setup

1. Audience votes via **SHOWING** on their phones
2. Votes are written to a Google Sheet
3. The sheet is published as CSV
4. This visualization is opened on a projector / screen
5. Counting starts with `open=now`
6. Visualization freezes automatically when the window closes

---

## Why this design

* **Separation of concerns**

  * phones write events
  * spreadsheet stores history
  * visualization interprets time

* **Replayable**
  The same data can be re-counted with different windows later.

* **Robust**
  No backend, no credentials, no state on the server.

---

## Notes

* Google’s “Publish to web” CSV can lag a few seconds — this is normal.
* Cache-busting is built in to avoid stale data.
* This visualization is read-only; it never writes data.

---

## Related

* [**SHOWING**](https://github.com/AdrianArtacho/SHOWING) — participatory smartphone interface
* [Google Apps Script](https://script.google.com/u/0/home/projects/1FiYznFwYCy__RUDMTI3nTsVvCZMp07lOhq0CHe4nFKAhoLyB7qR4b5bp/edit) — vote collection backend
* [Google Sheets](https://docs.google.com/spreadsheets/d/1TrI3nvk_g4Bp6fpA5pEsfs-aozttzLySrd-5YcjnOW4/edit?gid=98746072#gid=98746072) — event log and analysis
* [URL Encoder online](https://adrianartacho.github.io/UrlEncode/)
