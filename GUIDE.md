# Podcast factory guide

Works with any agent that can run on a schedule and use connectors; steps that need a shell say so. Research and production can be one agent or two.

Each step has a question for the owner (if any), an action, and a pass-fail check. A step is done only when its proof passes. Rerun the proofs anytime to see what still works.

The goal is a feed curated to your lens that becomes your own podcast. Research sweeps written sources (news, blogs, changelogs, papers, social searches, forums) and podcast episodes against one lens; production narrates the written hits and plays real clips for podcast moments. Clips are on by default whenever Production step A1 (and CHECKLIST step 0) passes. If the agent cannot run commands, podcast moments are narrated too, with a link to the episode at the timestamp. Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.

---

# Research

Works with any agent that can run commands, keep files between runs, and run on a timer.

## R1. Purpose

- Question: in one sentence, what do you want this feed to keep you ahead on? Who else listens?
- Action: save the sentence as the first line of `lens.md` (copy [templates/lens.md](templates/lens.md)).
- Pass-fail: `lens.md` exists and starts with a single purpose sentence.

## R2. Draft the lens

- Action: turn the purpose into 2–5 named angles. For each one, write what counts as a hit, what counts as a near-miss, and what to ignore.
- Pass-fail: every angle in `lens.md` has all three parts filled in.
- Keep the lens editable. It's the owner's file, not the agent's.

## R3. Seed examples, both ways

- Question: name 2–3 moments (from any show or article) you'd want flagged, and 2–3 you'd want skipped.
- Action: save each one in `examples/` with the quote, the source link, the angle, and the expected verdict.
- Pass-fail: every angle has at least one "should flag" and one "should skip" example, and rescoring each example gives its expected verdict.

## R4. Sources

- Question: where does news on these topics show up for you today: sites, blogs, changelogs, newsletters, people or searches on social, forums, and shows you listen to? The agent then suggests a few more of each kind that fit the lens.
- Action: list each source in `sources.md` (copy [templates/sources.md](templates/sources.md)). Written sources get a kind, how to check them (feed, page or search query), and a cadence. Shows get a feed URL, a transcript source and a cadence, with transcripts tried in this order: the publisher's own transcript (for example the RSS `<podcast:transcript>` tag or the show's site), then speech-to-text on the RSS audio (Whisper), then YouTube captions as a last resort.
- Pass-fail: every source returns a dated item under 30 days old, and every show has one transcript actually fetched and saved. Drop any source that fails, and keep at least one written source alongside the shows.

## R5. Corpus with a watermark

- Action: save every transcript as a timestamped file (segments with start times), named by show and date, and record which episodes are already ingested. Keep a watermark per written source too (newest item seen), and a coverage log of every item scored.
- Pass-fail: rerunning ingest on unchanged sources adds zero files or log rows, and a new episode or article adds exactly one.

## R6. Calibration round

- Action: score the last week of written items and the last 5–10 episodes against the lens. Show the owner 8–10 moments, mixing ones flagged and ones passed on, and ask for hit, miss or "meh" on each.
- Pass-fail: every disagreement is saved to `examples/` as a new case, and rescoring all examples gives their expected verdicts.

## R7a. Sweep written sources

- Action: on each scheduled run, check every written source for items newer than its watermark. Open each candidate and read the item itself, not the headline or a summary of it. Score it once per lens angle (hit, strong-minor, minor or nothing) with a one-line reason, and skip anything an earlier run already covered.
- Pass-fail: every written source has a check logged for the run with a new watermark, every opened item has a verdict per angle, and no hit repeats a source link already in the coverage log.

## R7b. Read every new episode in full

- Action: on each scheduled run, read each new episode once per lens angle. Write a coverage record for it with a verdict per angle (hit, strong-minor, minor or nothing), timestamps and a one-line reason, even when nothing hits.
- Pass-fail: every ingested episode has a coverage record, and every angle in it has a verdict. A missing record means the episode wasn't read.

## R8. Hand clips to audio

- Action: write one queue item per hit. A written hit becomes a `story`: source, headline, published date, the verified facts the narration may use, the link, and why it matters. A podcast hit becomes a `clip`: the show, the episode audio URL, the verbatim quote copied from the transcript, the approximate start and end (60–150 seconds), the episode link plus timestamp, and why it matters.
- Pass-fail: a schema check against [templates/queue-item.schema.json](templates/queue-item.schema.json) passes on every queue item, every story fact can be found on its source page, every clip quote appears word for word in the saved transcript near the given start, and no two items share a source link (plus timestamp for clips).

## R9. Hosts are anecdotes

- Action: label each claim as first-hand data, a vendor claim, or a host's opinion or anecdote, and never upgrade one to another in the summary or the script.
- Pass-fail: every queue item has a claim-type field.

## R10. Corrections become lens rules

- Action: when the owner says "that wasn't a hit" or "you missed this," add the moment to `examples/` and, if it's a pattern, a general rule to `lens.md`.
- Pass-fail: the correction's example rescores to the owner's verdict on the next run.

## R11. Quiet runs stay quiet

- Action: notify the owner only when a run finds a hit. Otherwise just log the run.
- Pass-fail: the run log has a row for every scheduled run, and notifications match runs that found hits.

---

# Production

Works with any agent that can run on a schedule and use connectors; steps that need a shell say so.

## A1. Can this agent run commands and keep files?

- Action: run `ffmpeg -version`, write a small test file, and read it back in a new session.
- Pass-fail: a version string is printed, and the file's contents match after the restart.
- If it fails: run the audio half on a small server or laptop with a timer (cron), and keep research in the agent. Narration with timestamped source links is the fallback until this check passes.

## A2. Who listens, and how long?

- Question: just you, or a team too? How long is the commute or listening window? How often (weekdays, daily, weekly)?
- Action: record the answers in `settings` (audience, target minutes, hard maximum, cadence).
- Pass-fail: the settings file exists with all four fields filled in.
- Note: with more than one listener, narrate in the third person and never address one person.

## A3. Pick one voice

- Action: render the same 2–3 sentence paragraph in 2–3 candidate voices on the owner's text-to-speech account and play them.
- Pass-fail: the chosen voice is saved in settings, and a sample MP3 of that voice plays from start to finish.
- Keep samples short; narration is the main ongoing cost.

## A4. Narration render

- Action: split the script at section breaks into chunks under the voice service's per-call limit, render each one, then join them with short crossfades (for example ffmpeg `acrossfade`, MP3 at 128 kbps, 44.1 kHz).
- Pass-fail: the final MP3's duration is within 10% of the script's estimated length, and there are no silent gaps over 2 seconds.

## A5. Clips from real episodes

- Clips are on by default whenever A1 passes. If the agent can't run commands, fall back to narration that summarizes the point and links to the episode at the timestamp.
- Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.
- Input from research: show, episode audio URL (from the RSS enclosure), verbatim quote, approximate start and end, source link.
- Action: download the episode, transcribe only about 3 minutes around the approximate start (Whisper), find the quote, then cut 60–150 seconds with short fades. Published timestamps drift because ads are inserted dynamically, so always re-align.
- Pass-fail: the clip's own transcript contains the opening words of the quote within its first 5 seconds.
- If the quote can't be found: drop the clip and narrate the point with a link instead. Never cut on the unaligned timestamp.

## A6. Script rules

- Every claim names its source, and anything without one is cut.
- Written items are narrated from their verified facts only, naming the source in the sentence.
- Each clip gets a short spoken intro saying why it matters to the listener's lens.
- Lead with the strongest hit, whatever its kind, and mix stories and clips rather than grouping all clips at the end.
- Plain words: explain any technical term in one sentence the first time it comes up.
- Pass-fail: a check confirms every script item maps to a queue item with a source link.

## A7. No repeats

- Action: append every aired item (source link, plus quote for clips) to an aired log, and check it before each build.
- Pass-fail: the new episode has zero items that match the aired log.

## A8. Cost cap

- Question: what's the monthly budget for the voice service?
- Action: log characters or credits used per episode, and skip or shorten the build when the month's projected total would pass the cap.
- Pass-fail: the cost log has a row for every episode, and the running total is under the cap.

## A9. Private delivery

- Action: publish to a private feed at an unguessable link (or send it to the owner's chat), with the transcript next to each episode.
- Pass-fail: the newest episode plays from the feed in a podcast app, and its transcript opens.
- If the feed includes clips of other people's shows, keep it to your own listening unless you've decided your wider use is fair.

## A10. Pilot and calibration

- Action: build one short pilot episode from the sources chosen at setup, then ask the owner what to change.
- Pass-fail: the owner's feedback is saved as general principles in `style-guide.md` (copy [templates/style-guide.md](templates/style-guide.md); not narrow "don't say X" rules), and the next build reads that file.
