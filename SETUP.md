# Setup

Walk this once when you start. Each step below points at a proof in [CHECKLIST.md](CHECKLIST.md). The full actions live in [GUIDE.md](GUIDE.md). A step is done only when its proof passes.

Copy the starter files from [templates/](templates/) and fill them in: [lens.md](templates/lens.md), [sources.md](templates/sources.md), [style-guide.md](templates/style-guide.md), and the queue-item schema at [templates/queue-item.schema.json](templates/queue-item.schema.json).

Clips are on by default whenever CHECKLIST step 0 shows that the agent can run commands, keep files, and run `ffmpeg`. When that check fails, podcast moments are narrated too, with a link to the episode at the timestamp. Written sources are always narrated. Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.

## 0. See what this agent can actually do

Run [CHECKLIST.md](CHECKLIST.md) step 0 on the agent you plan to use. Record which parts passed: commands, files that survive a new session, `ffmpeg`, and a scheduled run.

- If commands, persistent files, and `ffmpeg` all pass, leave clips on.
- If any of those fail, build with narration and timestamped source links. You can still run the audio half on a small server or laptop with a timer (cron) and keep research in the agent, as in GUIDE A1.
- If the timer check fails, you can still do a first run by hand; unattended operation is not proven yet.

## 1. Write the purpose and the lens

Ask what this feed should keep you ahead on, and who else listens. Copy [templates/lens.md](templates/lens.md) to `lens.md`. Save the purpose as the first line, then turn it into 2–5 named topics. For each topic, write what counts as a hit, what counts as a near-miss, and what to ignore. Keep `lens.md` editable; it is the owner's file, not the agent's.

Proofs: CHECKLIST R1 and R2, and GUIDE R1–R2.

## 2. Seed examples, both ways

Name 2–3 moments you would want flagged and 2–3 you would want skipped. Start from the test-case section in `lens.md`, then save each one in `examples/` with the quote, the source link, the topic, and the expected verdict. Every topic needs at least one "should flag" and one "should skip" example, and rescoring each example must give its expected verdict.

Proofs: CHECKLIST R3, and GUIDE R3.

## 3. Add sources and ingest a corpus

Ask where news on these topics shows up today: sites, company blogs, changelogs, newsletters, people or searches on social, forums, and shows. The agent suggests a few more of each kind that fit the lens. Copy [templates/sources.md](templates/sources.md) to `sources.md`. Give each written source a kind, how to check it (feed, page, or search query), and a cadence. Give each show a feed URL, a transcript source, and a cadence, trying transcript sources in this order: the publisher's own transcript (for example the RSS `<podcast:transcript>` tag or the show's site), then speech-to-text on the RSS audio (Whisper), then YouTube captions as a last resort.

Drop any source with no dated item in the last 30 days, and any show that does not yield a saved transcript. Keep at least one written source alongside the shows. Then ingest with a watermark per source so reruns do not write duplicates.

Proofs: CHECKLIST R4 and R5, and GUIDE R4–R5.

## 4. Calibrate on recent items

Score the last week of written items and the last 5–10 episodes against the lens. Show the owner 8–10 moments, mixing ones flagged and ones passed on, and get a hit, miss, or "meh" on each. Save every disagreement to `examples/` and confirm that rescoring matches the expected verdicts.

Proofs: CHECKLIST R6, and GUIDE R6.

## 5. Record who listens, how long, and the voice budget

Save audience, target minutes, hard maximum, and cadence in `settings`. If more than one person listens, narrate in the third person and never address one person. Render the same short paragraph in 2–3 candidate voices on the owner's text-to-speech account, pick one, and save it. Ask for a monthly voice-service budget and start the cost log.

Keep the voice samples short; narration is the main ongoing cost.

Proofs: CHECKLIST A2, A3, and A8, and GUIDE A2, A3, and A8.

## 6. Set up keys, storage, the private feed and the schedule

Store the voice-service and feed-host keys safely and create the state folder. Then stand up the private podcast feed: hosted audio and transcripts under an address with a random token, plus an RSS feed the owner subscribes to once in their own podcast app. Chat delivery is only the fallback for agents that cannot publish files. If more than one agent or script will contribute research, stand up the intake API from [ARCHITECTURE.md](ARCHITECTURE.md) C2 and give each contributor its own key. Last, ask when the owner starts listening and schedule research and then production to finish before that time.

Proofs: CHECKLIST A9, A11, A12 and A13, and GUIDE A9, A11, A12 and A13.

## 7. Build a short pilot, then write the style guide

Build one short pilot from the sources chosen above, using clips if step 0 passed and narration with timestamped links if it did not. Ask the owner what to change. Copy [templates/style-guide.md](templates/style-guide.md) to `style-guide.md`, save the feedback as general principles (not narrow "don't say X" rules), and make sure the next build reads that file. Queue items handed from research to audio should pass [templates/queue-item.schema.json](templates/queue-item.schema.json).

Proofs: CHECKLIST A10 and R8, and GUIDE A10 and R8.

## After setup

Rerun [CHECKLIST.md](CHECKLIST.md) anytime — not only on the first day — to see what still works. Ongoing runs follow GUIDE R7a–R11 and A4–A8.
