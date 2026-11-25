# Checklist

Every item names its proof, not just the step. A step is done only when its pass-fail check passes. Rerun this list anytime — first setup or months later — to see what still works.

Do not check off a label like "sources set up." Check the proof, for example: each source returns a dated item from the last 30 days, and each show has a transcript file on disk.

Each step is a question (when the owner must answer), an action, and a pass-fail check.

Clips are on by default whenever step 0 shows that the agent can run commands, keep files, and run `ffmpeg`. When that check fails, podcast moments are narrated too, with a link to the episode at the timestamp. Written sources are always narrated. Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.

The detailed actions are in [GUIDE.md](GUIDE.md). Use [SETUP.md](SETUP.md) for the first-time order.

---

## 0. Platform capability

Run this first on any platform, including ones in the README platform cards. Record each sub-check separately; clips depend on more than "the agent is set up."

- Question: which agent and which surface will run this (for example desktop app, web chat, or a small server)?
- Action: run `ffmpeg -version`; write a small test file and read it back in a new session; schedule a one-time or short-interval run that appends a row to a run log.
- Pass-fail:
  - Commands and files: a version string is printed, and the file's contents match after the restart.
  - Timer: the scheduled run leaves a log row at the expected time.
- If `ffmpeg` or file persistence fails: clips are off; use narration with timestamped source links. You may run the audio half on a small server or laptop with a timer (cron) and keep research in the agent.
- If only the timer fails: you can still build by hand; unattended operation is not proven.

---

## First run

Complete these once during setup, then rerun them whenever you change the agent, the lens, the sources, the voice, or the delivery path.

### R1. Purpose

- Question: in one sentence, what do you want this feed to keep you ahead on? Who else listens?
- Action: save the sentence as the first line of `lens.md` (start from [templates/lens.md](templates/lens.md)).
- Pass-fail: `lens.md` exists and starts with a single purpose sentence.

### R2. Draft the lens

- Action: turn the purpose into 2–5 named angles. For each one, write what counts as a hit, what counts as a near-miss, and what to ignore.
- Pass-fail: every angle in `lens.md` has all three parts filled in.

### R3. Seed examples, both ways

- Question: name 2–3 moments (from any show or article) you'd want flagged, and 2–3 you'd want skipped.
- Action: save each one in `examples/` with the quote, the source link, the angle, and the expected verdict.
- Pass-fail: every angle has at least one "should flag" and one "should skip" example, and rescoring each example gives its expected verdict.

### R4. Sources

- Question: where does news on these topics show up for you today: sites, blogs, changelogs, newsletters, social, forums, and shows? The agent then suggests a few more of each kind.
- Action: list each source in `sources.md` (start from [templates/sources.md](templates/sources.md)): written sources with kind, how to check, and cadence; shows with feed URL, transcript source, and cadence.
- Pass-fail: every source returns a dated item under 30 days old, and every show has one transcript actually fetched and saved. Drop any source that fails, and keep at least one written source alongside the shows.

### R5. Corpus with a watermark

- Action: save every transcript as a timestamped file, named by show and date; keep a watermark per written source and a coverage log of every item scored.
- Pass-fail: rerunning ingest on unchanged sources adds zero files or log rows, and a new episode or article adds exactly one.

### R6. Calibration round

- Action: score the last week of written items and the last 5–10 episodes against the lens. Show the owner 8–10 moments, mixing ones flagged and ones passed on, and ask for hit, miss or "meh" on each.
- Pass-fail: every disagreement is saved to `examples/` as a new case, and rescoring all examples gives their expected verdicts.

### A2. Who listens, and how long?

- Question: just you, or a team too? How long is the commute or listening window? How often (weekdays, daily, weekly)?
- Action: record the answers in `settings` (audience, target minutes, hard maximum, cadence).
- Pass-fail: the settings file exists with all four fields filled in.

### A3. Pick one voice

- Action: render the same 2–3 sentence paragraph in 2–3 candidate voices on the owner's text-to-speech account and play them.
- Pass-fail: the chosen voice is saved in settings, and a sample MP3 of that voice plays from start to finish.

### A8. Cost cap

- Question: what's the monthly budget for the voice service?
- Action: start the cost log and record the cap in settings.
- Pass-fail: the cost log exists, the cap is recorded, and the running total is under the cap.

### A9. Delivery

- Action: publish to a private feed at an unguessable link (or send it to the owner's chat), with the transcript next to each episode.
- Pass-fail: the newest episode plays from the feed in a podcast app, and its transcript opens.

### A10. Pilot and calibration

- Action: build one short pilot episode from the sources chosen at setup, then ask the owner what to change.
- Pass-fail: the owner's feedback is saved as general principles in `style-guide.md` (start from [templates/style-guide.md](templates/style-guide.md); not narrow "don't say X" rules), and the next build reads that file.

---

## Every run

Rerun these on a schedule, and anytime you want to know what still works.

### R5. Watermark still holds

- Action: run ingest on the current sources.
- Pass-fail: unchanged sources add zero files or log rows; each new episode or article adds exactly one.

### R7a. Sweep written sources

- Action: check every written source past its watermark, read each candidate item itself, and score it per lens angle with a one-line reason.
- Pass-fail: every written source has a check logged for this run, every opened item has a verdict per angle, and no hit repeats a link already in the coverage log.

### R7b. Read every new episode in full

- Action: read each new episode once per lens angle and write a coverage record, even when nothing hits.
- Pass-fail: every ingested episode has a coverage record, and every angle in it has a verdict. A missing record means the episode wasn't read.

### R8. Queue items are complete

- Action: write one queue item per hit: a `story` for a written hit (headline, date, verified facts, link, why it matters) or a `clip` for a podcast hit (audio URL, verbatim quote, approximate start and end of 60–150 seconds, episode link plus timestamp, why it matters).
- Pass-fail: a schema check against [templates/queue-item.schema.json](templates/queue-item.schema.json) passes on every queue item, every story fact can be found on its source page, every clip quote appears word for word in the saved transcript near the given start, and no two items share a source link.

### R9. Hosts are anecdotes

- Action: label each claim as first-hand data, a vendor claim, or a host's opinion or anecdote, and never upgrade one to another.
- Pass-fail: every queue item has a claim-type field.

### R10. Corrections become lens rules

- Action: when the owner says "that wasn't a hit" or "you missed this," add the moment to `examples/` and, if it's a pattern, a general rule to `lens.md`.
- Pass-fail: the correction's example rescores to the owner's verdict on the next run.

### R11. Quiet runs stay quiet

- Action: notify the owner only when a run finds a hit. Otherwise just log the run.
- Pass-fail: the run log has a row for every scheduled run, and notifications match runs that found hits.

### A4. Narration render

- Action: split the script at section breaks, render each chunk, and join them with short crossfades.
- Pass-fail: the final MP3's duration is within 10% of the script's estimated length, and there are no silent gaps over 2 seconds.

### A5. Clips (only if step 0 passed)

- Action: download the episode, transcribe only about 3 minutes around the approximate start, find the quote, then cut 60–150 seconds with short fades. Always re-align; never cut on an unaligned timestamp.
- Pass-fail: the clip's own transcript contains the opening words of the quote within its first 5 seconds. If the quote can't be found, the clip is dropped and the point is narrated with a link instead.
- If step 0 failed: skip this check. Pass-fail instead: every script item that would have been a clip is narrated and has a timestamped source link.

### A6. Script rules

- Action: cut anything without a source; narrate written items from their verified facts only; intro each clip with why it matters; mix stories and clips by strength; explain technical terms in one sentence the first time they come up.
- Pass-fail: a check confirms every script item maps to a queue item with a source link.

### A7. No repeats

- Action: append every aired item (source link, plus quote for clips) to an aired log, and check it before each build.
- Pass-fail: the new episode has zero items that match the aired log.

### A8. Cost cap still holds

- Action: log characters or credits used for this episode, and skip or shorten the build if the month's projected total would pass the cap.
- Pass-fail: the cost log has a row for every episode, and the running total is under the cap.

### A9. The newest episode still plays

- Action: open the feed or chat delivery and the companion transcript.
- Pass-fail: the newest episode plays, and its transcript opens.
