# Podcast factory blueprint

Turn the slice of the world you care about into your own daily podcast.

It starts with your **lens**: a short file you write that says what you're trying to stay ahead on and why, with a few real examples of things you'd want flagged and things you'd want skipped. Every item the agent reads gets judged against it, and only what clears it makes the episode. The examples are what make it a lens rather than a topic list.

Once the lens exists, an agent sweeps the places that news actually shows up: news sites, company blogs, changelogs, papers, social posts, forums, and the podcasts you already follow. It scores everything against your written lens, throws out what doesn't matter, and builds one short episode for your commute or review window. Most items are narrated in a single voice. When the best take on something is a person saying it on a show, you hear the real clip.

The point is the curation, not the audio. A generic news digest is easy to get. A feed that knows what you care about, learns from your corrections, and stays quiet on slow days is the part worth building.

## How it works

1. **Lens.** The file defined above: one purpose sentence and 2–5 named topics, each with what counts as a hit, a near-miss, and noise, plus real examples both ways. You own this file and it gets sharper every time you correct a verdict.
2. **Sweep.** On a schedule, the agent checks every source in `sources.md`: written sources for new items since the last run, and podcast feeds for new episodes, which it transcribes and reads in full.
3. **Score.** Every candidate gets a verdict per topic, with a one-line reason, and only hits become queue items. Claims keep their type: first-hand data, a vendor claim, or someone's opinion.
4. **Build.** Audio turns the queue into one episode: narrated segments for written items, real clips with a short spoken intro for podcast moments, and nothing that already aired.
5. **Calibrate.** You mark items hit, miss, or meh. Each correction becomes a test case and, if it's a pattern, a rule in the lens.

Research and production can be one agent or two. The steps name files and proofs, not a particular vendor's workflow.

## Clips and narration

Clips are on by default whenever the capability check in [CHECKLIST.md](CHECKLIST.md) step 0 passes (the agent can run commands, keep files, and run `ffmpeg`). When it doesn't, podcast moments get narrated too, with a link to the episode at the timestamp.

Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.

## What's in this package

- [SETUP.md](SETUP.md) — first-time walkthrough, in order.
- [GUIDE.md](GUIDE.md) — the Research and Production steps, each with a question (when the owner must answer), an action, and a pass-fail check.
- [CHECKLIST.md](CHECKLIST.md) — the same proofs in a list you can rerun anytime to see what still works.
- [templates/](templates/) — starter files to copy and fill in:
  - [templates/lens.md](templates/lens.md) — purpose, topics, and flag/skip test cases
  - [templates/sources.md](templates/sources.md) — written sources (sites, blogs, changelogs, papers, searches) and shows, each with how to check it
  - [templates/style-guide.md](templates/style-guide.md) — general principles the next build must read
  - [templates/queue-item.schema.json](templates/queue-item.schema.json) — required fields for each hit handed to audio, as a narrated story or a clip

A step is done only when its proof passes. Do not check off a label like "sources set up." Check the proof: each source returns a dated item from the last 30 days, and each show has a transcript file on disk.

## Platform cards

Statuses:

- **Tested** — we ran the first-run check ourselves. Only Grok Bot has this status.
- **Documented** — vendor or press says so, with a link.
- **Unverified** — we have not confirmed it, and we do not have a vendor or press source for the claim.

Use the cards to pick a starting place. Features, plans, and sandboxes change, so treat every card as a hint, not a guarantee.

### Claude Duet

- Status: **Documented**
- Anthropic's Claude Duet has been in research preview since October 21, 2026. Press reports it has its own virtual machine with a browser; carries over memory, skills, and connectors from Claude; sends a morning brief; checks connected apps every few hours; and asks for approval before sending. ([VentureBeat](https://venturebeat.com/technology/anthropics-answer-to-muse-is-here-claude-duet-a-mobile-and-desktop-ai-agent-that-takes-actions-proactively); [The Verge hands-on](https://www.theverge.com/ai-artificial-intelligence/1032518/claude-duet-ai-agent-hands-on))
- Shell access, `ffmpeg`, and persistent files: **Unverified** (not documented in those sources).
- Run CHECKLIST step 0 to confirm.

### Grok Bot

- Status: **Tested**
- We ran CHECKLIST step 0 ourselves: the agent ran commands, wrote a file and read it back in a new session, printed an `ffmpeg` version string, and a scheduled run left a log row. Clips stay on under the default above.
- Run CHECKLIST step 0 to confirm.

### Dots

- Status: **Documented**
- OpenAI's Dots (also called ChatGPT dots) are always-on agents with their own cloud computer and browser. They use ChatGPT memory and connected apps, keep working between conversations, and ask for approval on sensitive actions. ([Introducing dots](https://openai.com/index/introducing-dots/); [Meet dots](https://learn.chatgpt.com/docs/dots); [Getting started](https://help.openai.com/en/articles/20001530-getting-started-with-your-dot))
- Shell access, `ffmpeg`, and a durable on-disk corpus: **Unverified**.
- Run CHECKLIST step 0 to confirm.

### Muse

- Status: **Documented**
- This card is Meta's personal AI agent Muse (announced September 8, 2026). The name is also used by other products (for example a Microsoft Research game model); those are not this card.
- Meta says Muse runs on Muse Secure VM, a dedicated virtual machine with its own browser; works across the Muse app and WhatsApp; keeps working after the app is closed; and asks for approval before it sends a message or makes a purchase. ([Introducing Muse](https://about.fb.com/news/2026/09/introducing-muse-personal-ai-agent/))
- Shell access, `ffmpeg`, and a durable on-disk corpus: **Unverified**.
- Run CHECKLIST step 0 to confirm.

### Other surfaces (short)

- **ChatGPT scheduled tasks** — **Documented.** Desktop tasks can run shell commands in a local project if the machine stays on; web tasks do not keep a local folder between runs. ([Scheduled tasks](https://learn.chatgpt.com/docs/automations)) Run CHECKLIST step 0 to confirm.
- **Claude Cowork / Claude Code** — **Documented.** Scheduled cloud tasks run shell commands in an isolated environment; desktop scheduled tasks can see local files. ([Cowork scheduled tasks](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork); [Claude Code scheduled tasks](https://code.claude.com/docs/en/routines)) Run CHECKLIST step 0 to confirm.
- **Gemini tasks** — **Documented.** Time-based schedules, plus a remote computer that can install audio tools if network access is on. ([Schedules](https://support.google.com/gemini/answer/17094710); [Remote computer](https://support.google.com/gemini/answer/18789153)) Run CHECKLIST step 0 to confirm.
- **Cursor Automations** — **Documented.** Cloud agents on a schedule or events; do not assume the working tree is a durable corpus. ([Automations](https://cursor.com/docs/cloud-agent/automations)) Run CHECKLIST step 0 to confirm.

If your agent is not listed, start at CHECKLIST step 0 anyway. The proofs are the same.
