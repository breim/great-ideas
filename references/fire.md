# great-ideas — fire mode

Generate a batch of signal-backed build opportunities for any market, sector, technology, or geography the user names. The skill maintains a running list in a markdown file: every invocation reads what is already there, pulls fresh signal from the open web, applies a scope filter, and appends new ideas with continued numbering.

The non-negotiable rule: slop is worse than silence. Two strong, in-scope, signal-backed ideas beat eight filler ones. If real signal does not exist, drop the idea, never fabricate.

## Step 1: gather inputs

Always start with AskUserQuestion (or natural-language parsing if the user already provided everything in one shot). Do not silently assume defaults; surface them. Capture, in this rough order:

1. **Domínio / theme.** What technology, product category, platform constraint, or market defines the search. The user states this freely (examples: "Meta no-display AI glasses", "B2B fintech for SMBs", "voice AI devtools", "Shopify apps for DTC brands", "ferramentas para creators no TikTok"). Do not constrain.
2. **Escopo geográfico.** Global, a specific country list, or exclusions ("global only, exclude Brazil", "US + Canada", "Europa + UK"). Take the user literally. If they say "global, no Brazil", strip Brazil-only signals and Portuguese-language-only plays from the output unless they also work globally.
3. **Perfil do builder.** Solo founder, two-person team, agency with capacity, funded team. Defaults to solo builder if unstated.
4. **Horizonte de lucro.** 3-6 months, 6-12 months, 12+ months. Defaults to 3-6 months.
5. **Tamanho do batch.** How many ideas per fire (default 5 to 10, never more than 10 unless user insists; fewer is fine if signal is thin).
6. **Caminho do arquivo .md.** Absolute path to the markdown file that accumulates ideas. Create it if it does not exist. Suggest a sensible default inside the user's workspace folder if they want one picked.
7. **Scope filter rules.** Any hard constraints: "must work with X", "must not depend on Y", "allowed shapes only Z", "no in-lens UI", "no auth via OAuth", etc. These become hard filters; anything that fails is dropped before drafting.
8. **Custom sources.** Optional domain-specific places to look (specific subreddits, developer forums, niche newsletters, industry trade publications). Added on top of the default source list.

If the user dumped all of this up front in natural language (as they often do when invoking the skill with a long prompt), parse what is there and only ask about the gaps.

## Step 2: read the existing file

Read the output file end to end. Extract every existing idea title and the pain/buyer pair behind each. Never produce a duplicate or a near-duplicate (same pain + same buyer counts as a duplicate even with different surface wording). Note the highest existing number; new entries continue from there.

If the file does not exist, create it. Open with a small header that records the parameters captured in Step 1 (domain, scope, builder profile, horizon, scope filter), so future invocations and the user can both see the constitution of the list.

## Step 3: pull fresh signal

WebSearch the last 7 days only. Cite a specific source URL with a date per idea. Default source rotation, adapted to the domain:

- Product Hunt top of the week, filtered to the relevant category (wearables, voice AI, devtools, fintech, etc).
- r/SideProject, r/Entrepreneur, r/SaaS top posts. Hunt hard for "$X MRR" or "$X ARR" milestones; revenue posts are the strongest validation signal that exists.
- Domain-specific subreddits inferred from the theme. Examples: r/smartglasses + r/MetaGlasses + r/augmentedreality for wearables; r/fintech + r/PersonalFinance for fintech; r/devtools + r/programming for devtools; r/Notion + r/productivity for productivity tools; r/ecommerce + r/shopify for commerce. Pick what fits.
- Hacker News Show HN posts above 50 points relevant to the domain.
- IndieHackers public revenue posts and milestone threads.
- Exploding Topics + Google Trends rising queries for the domain (look for queries with rising slopes inside the last 90 days).
- App Store top charts in the user's geographic scope (not in excluded geographies): Productivity, Health & Fitness, Education, Utilities, plus whatever category fits the domain.
- Official developer blogs and forums when the domain involves a platform with its own dev program (Meta Wearables, Shopify Partners, Stripe, Vercel, OpenAI, Anthropic, Apple developer, Google Play Console, etc).
- Trade press and substacks if the domain is a regulated or industry-specific vertical (health, legal, finance).

Plus any custom sources from Step 1.

For every idea that survives Step 4, you must have at least one dated source URL from the last 7 days. If you cannot find one, the idea does not ship in this batch. This is the single most important rule of the skill.

## Step 4: apply scope filter

Drop, in this order:

1. Anything that violates the user's hard constraints from Step 1.
2. Anything you cannot back with a real, dated source URL.
3. Anything that duplicates or near-duplicates an existing entry in the file.
4. Anything that clusters into the same vertical as several others in this batch (require at least 3 distinct verticals per fire to avoid a one-note batch).
5. Anything pitched at a geography the user excluded.

Drops are not failures; they are how the skill stays honest. If filtering leaves fewer than 5 ideas, ship fewer; report what you dropped and why.

## Step 5: write the ideas

Use this exact template per idea. All fields required.

```
### N. <short, specific title>

- **Pain:** one or two sentences, concrete and personal
- **Why now:** what changed in the last 6 to 12 months that makes this winnable now
- **Buyer / ICP:** who pays, with role / segment / size when relevant
- **Validation signal:** the strongest evidence this is real (revenue post, search trend, complaint volume, waitlist)
- **Killer risk:** the single thing most likely to kill this, named honestly
- **Edge:** why the user's builder profile can win this vs. an incumbent or a well-funded team
- **Distribution reality:** how this actually ships given the constraints from Step 1 (companion app on the store, web app, dev preview org, partner program, browser extension, "wait for GA", etc)
- **Trend signal:** <source URL> | <YYYY-MM-DD> | one line of evidence in plain English
```

Add a **Modality** line only when the domain has a meaningful modality axis (e.g., for camera/audio wearables: camera-only / audio-only / camera+audio; for AI agents: voice / text / multimodal). Skip when not applicable; do not add empty fields.

Batch-level rules:

- At least 3 distinct verticals across the batch.
- Numbering continues from the highest existing entry in the file.
- 5 to 10 ideas, fewer is fine if real signal is thin.
- At least 2 ideas, when applicable, must be shippable today under the user's stated constraints (no "wait for GA" plays for both of them).

## Step 6: maintain the footer

At the very bottom of the file, maintain a single line that overwrites on each fire (do not stack):

```
Total ideas: N | Last fire: <ISO date> | Domain: <domain> | Scope: <geo> | Builder: <profile> | Horizon: <horizon>
```

Use bash `date -u +%Y-%m-%dT%H:%M:%SZ` for the ISO date if the shell is available; otherwise infer from the current date in context.

If the file already carries a `Last validate:` field from a previous validate run, preserve it in the new footer line — fire updates `Last fire:`, validate updates `Last validate:`, neither overwrites the other.

## Step 7: report to the user

Report concisely in chat:

- How many ideas were added, the new total in the file.
- Any sources that were unusually rich this round (e.g., "3 of 7 came from r/SideProject MRR threads").
- Anything you dropped on the scope filter and why, one line per drop, only if it helps the user calibrate the filter.
- A direct link to the file. If it lives inside the Cowork workspace folder, use a `computer://` link.

Do not paste the full ideas into chat. The file is the artifact; chat is a 4 to 6 line status.

## Stop conditions

Self-report and pause if any of these become true on an invocation:

- The file hits 100 distinct ideas. Suggest the user shift from collecting to executing (and consider running `/great-ideas validate` to surface the top entries).
- Two consecutive fires fail to produce 5 non-duplicate, in-scope, signal-backed ideas. The domain may be exhausted or the scope filter too narrow; surface this and offer to widen scope, swap sources, or pivot domain.
- The user says stop.

## Anti-patterns

- Fabricated trend signals. If the source is fictional, the idea is fictional. Drop it.
- Hand-waving validation ("people seem to want this"). Validation means a dated URL or a real number.
- Restating the same core idea in different verticals to pad the count.
- Pitching solutions for the excluded geography. If the user said "global, no Brazil", do not slip in Brazil-only signals or Portuguese-only plays unless they also work globally.
- Mixing in-scope and out-of-scope shapes. If the user said "no in-lens display", do not sneak in display-dependent ideas because they sound cool.
- Long preamble in chat. The file is the output; chat is a tight status.
- Generic ideas that could have been written without WebSearch. If the idea does not need the last 7 days of signal to exist, it is not a fire-worthy idea.

## Examples of good vs bad trend signals

**Good:** `https://www.reddit.com/r/SideProject/comments/.../my-1k-mrr-side-project-for-shopify-merchants | 2026-05-19 | Solo dev hit $1k MRR in 6 weeks selling a Shopify app that auto-tags returning customers by purchase pattern; 80 upvotes, 30 comments asking for the source.`

**Bad:** `Various sources | last week | People on Reddit seem interested in Shopify automation.`

The good one is checkable, dated, specific, and reveals both demand and a buyer profile. The bad one is unfalsifiable filler.

## Optional: schedule a recurring fire

If the user wants the skill to fire on a cadence (every 2 hours, daily, weekly), do not build that into this skill. Generate the first batch, then offer to create a scheduled task that re-invokes the skill with the same parameters. The scheduling system is a separate tool; keep this skill focused on a single high-quality batch per invocation.
