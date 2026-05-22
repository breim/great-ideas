# great-ideas

Signal-backed build opportunities, ranked into tiers. Does not brainstorm — mines.

## What it does

`great-ideas` is a Claude Code skill with three modes that share a single markdown file as their working surface.

- **fire mode** pulls live web signal from the last 7 days (Product Hunt, Reddit revenue posts, Hacker News, IndieHackers milestones, Google Trends, App Store charts, platform developer forums) and appends 5–10 concrete, in-scope, signal-backed opportunities to your ideas file. Every entry carries a dated source URL — no fabrication, no slop.
- **validate mode** reads that same file (no WebSearch) and ranks every entry into S/A/B/C tiers using a five-dimension rubric (validation depth, buyer clarity, time-to-ship, killer risk fatality, edge fit). It writes a `## Top N` section at the top of the file, replacing any prior one.
- **refire mode** runs multiple fire rounds back-to-back until the file reaches a target count N, rotating source emphasis per round to maximize coverage of the 7-day signal window. For when you want a burst, not a trickle.

You fire (or refire) to collect; you validate to decide what to build next.

## Install

This skill follows the standard Claude Code skill layout:

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

Drop the folder into your `~/.claude/skills/` directory (or install via the skills.sh registry once published) and Claude will pick up the `great-ideas` skill on the next session.

## Usage — fire mode

Invoke the skill with a domain or market. The skill will ask for what it needs and not what you already provided.

```
/great-ideas voice AI devtools, global excluding Brazil, solo builder, 3-6 month horizon, top 7, file=~/ideas/voice-ai.md
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

Each fire appends new ideas with continued numbering. See [`examples/ideas.example.md`](examples/ideas.example.md) for the shape of an entry and the parameter header.

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

## Anti-patterns the skill refuses

- Fabricated trend signals. Every fire entry needs a real, dated source URL.
- Hand-waving validation. "People want this" is not signal; a $X MRR Reddit post or a rising Google Trends slope is.
- Stuffing the batch with the same vertical to hit a number. Each fire requires at least 3 distinct verticals.
- Padding refire rounds to hit N. If signal is thin, refire stops; it never relaxes the quality bar.
- Inventing numeric scores in validate. The rubric is tier-based on purpose.
- Pasting the full output into chat. The file is the artifact; chat is a 4–6 line status (6–8 for refire).

## License

MIT.
