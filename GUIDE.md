# Podcast factory guide

Works with any agent that can run on a schedule and use connectors; steps that need a shell say so. Research and production can be one agent or two.

Each step has a question for the owner (if any), an action, and a pass-fail check. A step is done only when its proof passes. Rerun the proofs anytime to see what still works.

The goal is a feed curated to your lens that becomes your own podcast. Research sweeps written sources (news, blogs, changelogs, papers, social searches, forums) and podcast episodes against one lens; production narrates the written hits and plays real clips for podcast moments. Clips are on by default whenever Production step A1 (and CHECKLIST step 0) passes. If the agent cannot run commands, podcast moments are narrated too, with a link to the episode at the timestamp. Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.

---

# Research

Works with any agent that can run commands, keep files between runs, and run on a timer.

**What a lens is.** Your lens is a short, editable file (`lens.md`) that tells the agent what matters to you. It has a one-sentence purpose, a few angles you care about, and for each angle what counts as a hit, what's a near-miss and what to ignore, plus real examples of each. The agent scores every item it reads against it. Nothing reaches your episode unless it clears the lens, and your corrections keep sharpening it.

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

## R8. Hand hits to audio

- Action: submit one queue item per hit to the intake API ([ARCHITECTURE.md](ARCHITECTURE.md) C2), or write it to `queue/` while there's only one agent. If intake answers 403 or 429, stop posting for the run, keep the rest locally, and resubmit them first next run. Write one queue item per hit. A written hit becomes a `story`: source, headline, published date, the verified facts the narration may use, the link, and why it matters. A podcast hit becomes a `clip`: the show, the episode audio URL, the verbatim quote copied from the transcript, the approximate start and end (60–150 seconds), the episode link plus timestamp, and why it matters.
- Pass-fail: a schema check against [templates/queue-item.schema.json](templates/queue-item.schema.json) passes on every queue item, every story fact can be found on its source page, every clip quote appears word for word in the saved transcript near the given start, and no two items share a source link (plus timestamp for clips).

## R9. Opinions are anecdotes

- Action: label each claim as first-hand data, a vendor claim, or opinion and anecdote (from a host, writer or poster), and never upgrade one to another in the summary or the script.
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

- Action: append every aired item to `aired.jsonl` by its dedupe key (the same key intake uses: for a clip, the audio URL plus the start second), and check it before each build.
- Pass-fail: the new episode has zero items that match the aired log.

## A8. Cost cap

- Question: what's the monthly budget for the voice service?
- Action: log characters or credits used per episode, and skip or shorten the build when the month's projected total would pass the cap.
- Pass-fail: the cost log has a row for every episode, and the running total is under the cap.

## A9. Private podcast feed (default delivery)

The default is a private podcast feed the owner subscribes to once, so each episode shows up in their usual podcast app next to everything else they listen to. Sending the file to the owner's chat is the fallback, only for agents that cannot publish files to the web.

- Action: build and host these. The provider and language are up to the agent.
  - Hosting: any static host or object storage that serves files over HTTPS, answers range requests (the `Range` header, which podcast apps use to seek and resume), and sends `Content-Type: audio/mpeg` for MP3s.
  - Address: put the feed and its files under a path containing a random token of at least 128 bits (for example 32 hex characters). Nothing links to it, the folder does not list its contents, and the feed carries `<itunes:block>Yes</itunes:block>` so directories skip it. The address works like a password, so keep it with the other secrets (A11) and never post it in chat or logs.
  - Feed file: RSS 2.0 with the iTunes and Podcasting 2.0 namespaces, a square cover image (1400–3000 px), and one `<item>` per episode with a title, `pubDate`, a stable unique `guid`, an `<enclosure>` with the URL, exact length in bytes and `type="audio/mpeg"`, `itunes:duration`, a description that lists the source links, and a `<podcast:transcript>` tag pointing at the episode's transcript.
  - Publishing: give each episode a file name that never changes (for example `2026-01-15-0600-slug.mp3`), upload the audio and transcript first, then write the new feed to a temporary name and replace the old one in a single step, so a podcast app never reads a half-written feed or an enclosure that isn't uploaded yet.
  - Rotation: if the address leaks, make a new token, move the files, and have the owner subscribe again.
- Pass-fail, every publish:
  - The feed parses as XML, and every `guid` is unique.
  - A `HEAD` request on each new enclosure returns 200, its `Content-Length` matches the enclosure length, and its `Content-Type` is `audio/mpeg`. A request for the first kilobyte with a `Range` header returns 206.
  - The transcript URL returns 200.
  - The feed's folder without the token returns 403 or 404, never a file list.
- Pass-fail, once at setup: the owner subscribes in their own podcast app, and the newest episode plays from start to finish.
- If the feed includes clips of other people's shows, keep it to your own listening unless you've decided your wider use is fair.

## A10. Pilot and calibration

- Action: build one short pilot episode from the sources chosen at setup, then ask the owner what to change.
- Pass-fail: the owner's feedback is saved as general principles in `style-guide.md` (copy [templates/style-guide.md](templates/style-guide.md); not narrow "don't say X" rules), and the next build reads that file.

## A11. Keys and secrets

- Question: which accounts does this need? Usually the voice service, write access to the feed host, and an intake key if other agents submit research.
- Action: keep each key in the agent's own secret store, or in a file outside any repo that only the agent's user can read (for example mode 600). Give scripts the key through an environment variable. Never put a key or the feed address in scripts, logs, queue items, transcripts or chat. Scope each key as narrowly as the provider allows, for example write access to the feed's one bucket or folder.
- Pass-fail: one read-only call per key succeeds (for example, list voices on the voice service, or list the feed folder), and a search of scripts, logs and published files for each key's value finds nothing.

## A12. Storage layout

- Action: keep all state in one folder that survives restarts, in a fixed layout. Something like this works:

```
factory/
  settings.json         audience, minutes, cadence, voice, cost cap
  lens.md  sources.md  style-guide.md
  corpus/<source>/      saved items and transcripts, with the watermark per source
  queue/<id>.json       one item per hit, valid against templates/queue-item.schema.json
  aired.jsonl           one line per aired item (A7)
  costs.csv             one row per episode (A8)
  runs.log              one row per scheduled run, research or production
  episodes/<date-slug>/ script.md, chunks/, clips/, episode.mp3, transcript.txt
  secrets/              keys (A11); never committed or published
```

- Keep anything you would miss out of folders the platform treats as disposable, such as caches or build output.
- Pass-fail: after a restart, the agent reads `settings.json`, the newest `runs.log` row and the newest `aired.jsonl` line, and rebuilding the feed from `episodes/` produces the same items as the published feed.

## A13. Scheduled runs

- Question: when does the owner usually start listening?
- Action: schedule research first and production after it, with enough lead time that the episode is published before that time. Hold a lock file while a run works, so a second trigger exits instead of building twice. If research found no new hits, production logs a skipped run and publishes nothing, with no filler episode.
- Pass-fail: on each scheduled day, `runs.log` shows a research row and then a production row (built or skipped), the newest episode's `pubDate` is before the listening time, and a test trigger started during a run exits because of the lock.

