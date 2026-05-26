<p align="center">
  <img src="docs/assets/banner.png" alt="great-ideas — signal-backed product gaps, ranked into tiers" width="100%">
</p>

<h1 align="center">Great Ideas</h1>

<p align="center">Signal-backed product gaps, ranked into tiers. Does not brainstorm — finds what's missing.</p>

## Quick start

Three commands.

**1. Install** — run once, then restart your Claude Code session:

```bash
npx skills add breim/great-ideas
```

**2. First fire** — generate a batch into a fresh file. Type this as a slash command:

```
/great-ideas B2B SaaS for SMBs, global, solo builder, top 5, file=~/ideas/saas.md
```

The skill will ask only for the parameters you didn't provide, then mine the last 30 days of web signal for unmet requests, 1-star review patterns, open feature-request issues, and unanswered popular questions — writing 5 signal-backed gap entries to `~/ideas/saas.md`.

**3. First validate** — once the file has entries, rank the top 3:

```
/great-ideas validate 3 ~/ideas/saas.md
```

A `## Top 3` section is written at the top of the same file. See **Usage** sections below for all modes (fire, validate, refire) and the `/loop` pattern for continuous mining.

## Why gap-based

Most "give me ideas" tools brainstorm: they recombine concepts the model already has and dress them up with confident prose. Other "opportunity" tools surface hits — products that already shipped, revenue posts, charts of what's trending — which is a list of opportunities that other people are *already* taking. `great-ideas` does neither. It mines orphan problems: real, dated evidence of a pain that recurs across forums, with no satisfactory product solving it (or only inadequate ones the buyer rejects on price, scope, or shape). If a gap signal source does not exist, the entry does not ship. If a quick check shows an existing product already nails the gap at the buyer's price, the entry is dropped. The skill stops at 2 entries rather than padding to 10. This trades volume for honesty: fewer ideas in the file, each one checkable in 30 seconds by clicking the source URL.

## What it does

`great-ideas` is a Claude Code skill with three modes that share a single markdown file.

- **fire mode** pulls live web signal from the last 30 days (Reddit unmet-request threads, 1-star and 2-star review patterns on app marketplaces, GitHub feature-request issues with high reactions and no merged PR, unanswered popular Stack Overflow questions, Ask HN threads, IndieHackers "anyone need" posts, Product Hunt comments calling out what recent launches don't do) and appends 5–10 concrete, in-scope, signal-backed gap entries to your ideas file. Every entry carries a dated source URL and identifies what people do today (the inadequate workaround) and why nobody has closed the gap yet.
- **validate mode** reads that same file (no WebSearch) and ranks every entry into S/A/B/C tiers using a five-dimension rubric (gap signal depth, workaround pain, buyer clarity, time-to-ship, gap durability). It writes a `## Top N` section at the top of the file, replacing any prior one.
- **refire mode** runs multiple fire rounds back-to-back until the file reaches a target count N, rotating source emphasis per round to maximize coverage of the gap-signal surface (forums → marketplaces → issue trackers → community threads). For when you want a burst, not a trickle.

You fire (or refire) to collect; you validate to decide what to build next.

## How it stays honest

- **Dated URLs are mandatory.** Every fire entry carries a real source URL within a 30-day window (with priority on the last 7). No URL → idea drops before the file is touched.
- **Existing-product check.** If a quick search reveals an existing product already solves the gap well at the named buyer's price, the entry is dropped. Half-solving products (wrong segment, missing the one needed feature, too expensive) leave the gap intact and are named in the `Existing workaround` field.
- **No padding to hit a count.** If a fire can only justify 4 entries, the file gets 4. Refire stops the moment two consecutive rounds come up thin. There is no quality knob to relax.
- **Structural dedup.** Pain + buyer is the unique key. The same gap pitched at a new vertical does not count as new.
- **Tier veto in validate.** An idea whose Gap signal is hand-wave cannot be S-tier, even if every other field is strong. Gap signal depth is a veto, not a weight.
- **Hard caps.** 10 ideas per single fire (user-overridable but discouraged), 100 ideas per file ceiling, 10 rounds max per refire.

## Usage — fire mode

Invoke with a domain or market. The skill asks only for gaps you didn't fill in.

```
/great-ideas B2B SaaS for SMBs, global excluding Brazil, solo builder, 3-6 month horizon, top 7, file=~/ideas/saas.md
```

A new entry in the file looks like this:

```markdown
### 2. Per-feature retention analytics for tiny B2B SaaS

- **Pain:** B2B SaaS founders ship 5 features a sprint but cannot tell which ones drive retention. They want feature-cohort retention without instrumenting a warehouse.
- **Existing workaround:** Mixpanel and Amplitude are priced for Series-B (founders post bills jumping 8x); PostHog is a kitchen-sink platform that takes a day to configure for one chart; most 2-person teams DIY a `events` table in Postgres and write SQL by hand, which means nobody actually looks at it.
- **Why nobody built this:** the niche (2–10 person teams, $5k–$100k MRR) is too small for funded analytics teams to chase, and existing PLG-analytics tools optimize for "everything for everyone" instead of "one chart that answers the retention question".
- **Why now:** Cloudflare Workers + ClickHouse Cloud crossed a pricing threshold in Q1 2026 where ingesting 10M events per month costs under $10. The "just-enough-analytics" niche became economically viable for a solo builder.
- **Buyer / ICP:** technical founders at B2B SaaS startups (2–10 person, $5k–$100k MRR) who have analytics anxiety but no data engineer.
- **Killer risk:** PostHog Cloud's free tier ships a "feature retention" preset and eats the wedge.
- **Distribution reality:** ships today as a hosted SaaS + 50-line npm SDK; no platform dependency.
- **Gap signal:** https://www.reddit.com/r/SaaS/comments/example-mixpanel-bill | 2026-05-20 | 180-upvote r/SaaS thread: "I ripped out Mixpanel after they 8x'd my bill — went from $1.8k to $14k/mo. What's everyone using?" 64 comments, top replies are "PostHog (but it's a lot)", "spreadsheet", and "I just stopped tracking". No "use X, it's perfect for this" answer surfaces. [example]
```

The skill captures (asking only for the gaps):

- **Domain / theme** — what technology, category, or market frames the search.
- **Geographic scope** — global, allow-list, or exclusions.
- **Builder profile** — solo, two-person, agency, funded team.
- **Profit horizon** — 3–6, 6–12, or 12+ months.
- **Batch size** — 5 to 10 ideas per fire.
- **File path** — where the running list lives.
- **Scope filter rules** — any hard constraints (e.g., "must work with Stripe", "no enterprise-only plays").
- **Custom sources** — optional domain-specific forums or newsletters.

Each fire appends new ideas with continued numbering.

## Usage — validate mode

After at least one fire has populated the file, validate ranks the entries.

```
/great-ideas validate
/great-ideas validate 5
/great-ideas validate 15 ~/ideas/saas.md
/great-ideas validate --top=20 --file=~/ideas/saas.md
```

A section like the following is written at the top of the file (replacing any prior Top N):

```markdown
## Top 5 — validated 2026-05-23T12:30:00Z

> Ranked from the entries below using the validate rubric. Re-run `/great-ideas validate N` to refresh. No WebSearch — reads the file only.

1. **#7 — Per-feature retention analytics for tiny B2B SaaS** `[S]` — 180-upvote r/SaaS thread on Mixpanel pricing exodus; workaround is "PostHog (but it's a lot)" or "I just stopped tracking"; ships today as hosted SaaS + tiny SDK.
2. **#3 — Stripe webhook auditor for indie SaaS** `[S]` — 47 Stripe-tag GitHub issues across 6 repos all asking for retry-failure visibility; today people grep dashboards by hand.
3. **#1 — Slack-native NPS collector for indie SaaS** `[A]` — strong gap signal and buyer, but distribution waits on Slack marketplace approval.
4. **#9 — Churn-attribution dashboard for SaaS** `[A]` — solid gap signal, gap durability is thin — ChurnZero is rumored to add AI categorization next quarter and closes the wedge.
5. **#12 — Onboarding email split-tester for SaaS** `[B]` — buyer plausible but gap signal is 60 days old and only one thread.

**Tier distribution (all 14 ideas in the file):** S: 2 · A: 4 · B: 5 · C: 3
**Drop notes:** #4 and #11 fell to C — duplicate buyer of #7.
```

The original ideas, their numbers, and their fields are not modified. The footer is updated with `Last validate: <iso>`.

## Usage — refire mode

When you want to fill the file to a target count in a single sitting (instead of running fire repeatedly):

```
/great-ideas refire 50 ~/ideas/saas.md
/great-ideas refire --target=30 --file=~/ideas/saas.md
```

Refire reuses the parameter header (domain, scope, builder profile, etc) already in the file, then runs fire rounds back-to-back, rotating sources per round (Reddit unmet-request threads → marketplace 1-star review patterns → GitHub issues and Stack Overflow → Ask HN and IndieHackers community threads → cycle) so each round mines a different slice of the gap-signal surface. Stops automatically when:

- the target N is reached,
- two consecutive rounds add fewer than 5 strong gap entries (signal exhausted — most gaps in the domain already have products, or the scope is too narrow),
- the file hits the global 100-idea ceiling, or
- a 10-round safety cap is reached.

Use refire when you want a burst — a Saturday morning session to take a file from 10 → 50 — instead of trickling fires over a week. Quality bar is the same as fire; refire will stop early rather than pad.

## Continuous mining with `/loop`

For trickle-mining over time (instead of a burst in one session), the Claude Code `/loop` skill can re-invoke `great-ideas` on a schedule with **zero changes** to this skill:

```
/loop 6h /great-ideas B2B SaaS for SMBs, global ex-BR, solo, top 10, file=~/ideas/saas.md
```

Each scheduled round re-reads the file, continues the numbering, and dedupes against existing entries. The file grows in the background. Cancel through your `/loop` skill's normal cancel command.

**When to use which:**

- **Refire** — you want N ideas now, in one sitting. One Claude session, source rotation built in.
- **/loop fire** — you want continuous mining over days or weeks. Multiple Claude sessions, fresh 30-day windows each round, higher token cost.
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
