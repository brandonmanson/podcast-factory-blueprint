# Podcast factory blueprint

A reusable way to have an agent keep you ahead on a beat: it reads the shows you already care about, scores new episodes against a written lens, and builds a short listening episode for your commute or review window.

This package is for anyone setting that up on an agent they already use. Research and production can be one agent or two. The steps name the files and proofs, not a particular vendor's workflow.

## Clips and narration

Clips are on by default whenever the capability check in [CHECKLIST.md](CHECKLIST.md) step 0 passes (the agent can run commands, keep files, and run `ffmpeg`). When it does not, the fallback is narration that summarizes the point and links to the episode at the timestamp.

Private listening is the assumption: short slices of other people's shows with your commentary around them. Publishing a feed of them is your own call.

## What's in this package

- [SETUP.md](SETUP.md) — first-time walkthrough, in order.
- [GUIDE.md](GUIDE.md) — the Research and Production steps, each with a question (when the owner must answer), an action, and a pass-fail check.
- [CHECKLIST.md](CHECKLIST.md) — the same proofs in a list you can rerun anytime to see what still works.
- [templates/](templates/) — starter files to copy and fill in:
  - [templates/lens.md](templates/lens.md) — purpose, topics, and flag/skip test cases
  - [templates/sources.md](templates/sources.md) — one block per show: feed URL, transcript source, cadence
  - [templates/style-guide.md](templates/style-guide.md) — general principles the next build must read
  - [templates/queue-item.schema.json](templates/queue-item.schema.json) — required fields for each hit handed to audio

A step is done only when its proof passes. Do not check off a label like "sources set up." Check the proof: each show has a feed URL that resolves, a dated episode from the last 30 days, and a transcript file on disk.

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
