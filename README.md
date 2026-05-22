# great-ideas

Signal-backed build opportunities, ranked into tiers. Does not brainstorm — mines.

## Quick start

Prerequisite: Claude Code installed.

**1. Install** — clone this repo, symlink it into your skills directory, then restart your Claude Code session:

```bash
git clone <your-repo-url> ~/code/great-ideas
ln -s ~/code/great-ideas ~/.claude/skills/great-ideas
```

**2. First fire** — generate a batch into a fresh file. Type this as a slash command:

```
/great-ideas voice AI devtools, global, solo builder, top 5, file=~/ideas/voice.md
```

The skill will ask only for the parameters you didn't provide, then mine the last 7 days of web signal and write 5 signal-backed ideas to `~/ideas/voice.md`.

**3. First validate** — once the file has entries, rank the top 3:

```
/great-ideas validate 3 ~/ideas/voice.md
```

A `## Top 3` section is written at the top of the same file. See **Usage** sections below for all modes (fire, validate, refire) and the `/loop` pattern for continuous mining.

## Why signal-backed

Most "give me ideas" tools brainstorm: they recombine concepts the model already has and dress them up with confident prose. `great-ideas` mines: every entry must point at a real, dated post — a revenue milestone, a complaint thread, a search trend slope, a Show HN above 50 points — from the last 7 days. If a signal source does not exist, the idea does not ship. The skill stops at 2 entries rather than padding to 10. This trades volume for honesty: fewer ideas in the file, each one checkable in 30 seconds by clicking the source URL.

## What it does

`great-ideas` is a Claude Code skill with three modes that share a single markdown file.

- **fire mode** pulls live web signal from the last 7 days (Product Hunt, Reddit revenue posts, Hacker News, IndieHackers milestones, Google Trends, App Store charts, platform developer forums) and appends 5–10 concrete, in-scope, signal-backed opportunities to your ideas file. Every entry carries a dated source URL.
- **validate mode** reads that same file (no WebSearch) and ranks every entry into S/A/B/C tiers using a five-dimension rubric (validation depth, buyer clarity, time-to-ship, killer risk fatality, edge fit). It writes a `## Top N` section at the top of the file, replacing any prior one.
- **refire mode** runs multiple fire rounds back-to-back until the file reaches a target count N, rotating source emphasis per round to maximize coverage of the 7-day signal window. For when you want a burst, not a trickle.

You fire (or refire) to collect; you validate to decide what to build next.

## How it stays honest

- **Dated URLs are mandatory.** Every fire entry carries a real source URL within a 7-day window. No URL → idea drops before the file is touched.
- **No padding to hit a count.** If a fire can only justify 4 entries, the file gets 4. Refire stops the moment two consecutive rounds come up thin. There is no quality knob to relax.
- **Structural dedup.** Pain + buyer is the unique key. The same idea pitched at a new vertical does not count as new.
- **Tier veto in validate.** An idea whose Trend signal is hand-wave cannot be S-tier, even if every other field is strong. Validation depth is a veto, not a weight.
- **Hard caps.** 10 ideas per single fire (user-overridable but discouraged), 100 ideas per file ceiling, 10 rounds max per refire.

## Usage — fire mode

Invoke with a domain or market. The skill asks only for gaps you didn't fill in.

```
/great-ideas voice AI devtools, global excluding Brazil, solo builder, 3-6 month horizon, top 7, file=~/ideas/voice-ai.md
```

A new entry in the file looks like this:

```markdown
### 2. Voice-first changelog assistant for devtool teams

- **Pain:** devtools founders hate writing release notes; they ship weekly and the changelog is always 3 versions behind, which kills upgrade velocity for their customers.
- **Why now:** Anthropic's tool use and OpenAI's voice mode both became cheap enough in Q1 2026 to do "dictate the changelog over breakfast" workflows; PR diff parsing is now a 50-line script.
- **Buyer / ICP:** founders or DX leads at devtools companies shipping weekly (often 1–5 person teams in their first 12 months).
- **Validation signal:** 220-upvote IndieHackers revenue post about a competing text-based tool ("changelog.ai") hitting $4k MRR in 8 weeks; clear unmet demand for the voice variant.
- **Killer risk:** changelog.ai or a funded competitor adds voice in a sprint.
- **Edge:** solo builder can ship the voice ingestion + diff binding in 2 weeks; funded teams will overbuild.
- **Distribution reality:** ships today as a GitHub App + companion iOS shortcut; no platform GA waits.
- **Trend signal:** https://www.indiehackers.com/post/example-changelog-mrr | 2026-05-21 | "changelog.ai hit $4k MRR — wish it had voice input from my phone"; 220 upvotes, 65 comments asking the same [example]
```

The skill captures (asking only for the gaps):

- **Domain / theme** — what technology, category, or market frames the search.
- **Geographic scope** — global, allow-list, or exclusions.
- **Builder profile** — solo, two-person, agency, funded team.
- **Profit horizon** — 3–6, 6–12, or 12+ months.
- **Batch size** — 5 to 10 ideas per fire.
- **File path** — where the running list lives.
- **Scope filter rules** — any hard constraints (e.g., "no in-lens display", "must work with Stripe").
- **Custom sources** — optional domain-specific forums or newsletters.

Each fire appends new ideas with continued numbering.

## Usage — validate mode

After at least one fire has populated the file, validate ranks the entries.

```
/great-ideas validate
/great-ideas validate 5
/great-ideas validate 15 ~/ideas/voice-ai.md
/great-ideas validate --top=20 --file=~/ideas/voice-ai.md
```

A section like the following is written at the top of the file (replacing any prior Top N):

```markdown
## Top 5 — validated 2026-05-23T12:30:00Z

> Ranked from the entries below using the validate rubric. Re-run `/great-ideas validate N` to refresh. No WebSearch — reads the file only.

1. **#7 — Auto-tagger for returning Shopify customers** `[S]` — $1k MRR in 6 weeks per the Reddit thread; named buyer (DTC ops leads, 50–200 person brands).
2. **#3 — Voice-first changelog assistant** `[S]` — 220 IndieHackers upvotes; specific ICP (devtools founders with weekly releases).
3. **#1 — Solo-friendly billing portal for AI APIs** `[A]` — strong signal and buyer, but waits on Anthropic billing API GA.
4. **#9 — Notion-to-podcast pipeline** `[A]` — solid signal, edge is thin vs. funded competitors.
5. **#12 — Compliance scanner for healthtech indie devs** `[B]` — buyer plausible but trend signal is 60 days old.

**Tier distribution (all 14 ideas in the file):** S: 2 · A: 4 · B: 5 · C: 3
**Drop notes:** #4 and #11 fell to C — duplicate buyer of #7.
```

The original ideas, their numbers, and their fields are not modified. The footer is updated with `Last validate: <iso>`.

## Usage — refire mode

When you want to fill the file to a target count in a single sitting (instead of running fire repeatedly):

```
/great-ideas refire 50 ~/ideas/voice-ai.md
/great-ideas refire --target=30 --file=~/ideas/voice-ai.md
```

Refire reuses the parameter header (domain, scope, builder profile, etc) already in the file, then runs fire rounds back-to-back, rotating sources per round (Product Hunt → Hacker News → App Store → developer forums → cycle) so each round mines a different slice of the same 7-day signal window. Stops automatically when:

- the target N is reached,
- two consecutive rounds add fewer than 5 strong signal-backed ideas (signal exhausted),
- the file hits the global 100-idea ceiling, or
- a 10-round safety cap is reached.

Use refire when you want a burst — a Saturday morning session to take a file from 10 → 50 — instead of trickling fires over a week. Quality bar is the same as fire; refire will stop early rather than pad.

## Continuous mining with `/loop`

For trickle-mining over time (instead of a burst in one session), the Claude Code `/loop` skill can re-invoke `great-ideas` on a schedule with **zero changes** to this skill:

```
/loop 6h /great-ideas voice AI devtools, global ex-BR, solo, top 10, file=~/ideas/voice.md
```

Each scheduled round re-reads the file, continues the numbering, and dedupes against existing entries. The file grows in the background. Cancel through your `/loop` skill's normal cancel command.

**When to use which:**

- **Refire** — you want N ideas now, in one sitting. One Claude session, source rotation built in.
- **/loop fire** — you want continuous mining over days or weeks. Multiple Claude sessions, fresh 7-day windows each round, higher token cost.
- **Single fire** — you want one curated batch and you'll come back later by hand.

## Output anatomy

See [`examples/ideas.example.md`](examples/ideas.example.md) for a complete sample file showing the parameter header, a Top N section, four numbered ideas in the full template, and the footer line.

The skill itself is laid out like this:

```
great-ideas/
├── SKILL.md
├── references/
│   ├── fire.md
│   ├── validate.md
│   └── refire.md
└── examples/
    └── ideas.example.md
```

`SKILL.md` is the dispatcher (it routes by the first argument after `/great-ideas`); each `references/*.md` carries the full protocol for one mode.

## Limitations

- **Needs WebSearch.** fire and refire are useless offline. validate works offline because it only reads the file.
- **Source URLs decay.** Dated URLs from a fire today may 404 in 90 days. validate does not re-check them; if you re-validate an old file, treat the rankings as "what we knew when the fire ran."
- **Tier ranking is judgment, not measurement.** The five-dimension rubric in validate is Claude grading what is written in the file — it is not an independent fact-check of the sources.
- **Domain exhaustion is real.** Niches without fresh signal every 7 days will hit the "two thin rounds = stop" rule in refire. The chat will tell you when this happens.
- **No private-data integration.** The skill mines public web signal. It does not read your Linear, Slack, or customer interviews.

## License

MIT — see [LICENSE](LICENSE).
