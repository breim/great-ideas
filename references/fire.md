# great-ideas — fire mode

Generate a batch of signal-backed **gap** opportunities for any market, sector, technology, or geography the user names. A gap is a real, recurring pain whose solution does not yet exist, or exists only in a form that the buyer rejects (too expensive, too complex, missing the one feature they need, wrong shape for their workflow). The skill maintains a running list in a markdown file: every invocation reads what is already there, pulls fresh signal from the open web, applies a scope filter, and appends new gap entries with continued numbering.

The non-negotiable rule: slop is worse than silence. Two strong, in-scope, signal-backed gaps beat eight filler ones. If real signal does not exist, drop the idea, never fabricate.

**This mode hunts gaps, not hits.** A launched product with traction is the wrong kind of signal here — that opportunity is already taken. Hunt for the orphan problems around and underneath the hits: the comment threads asking for what nobody shipped, the 1-star reviews complaining about the same missing feature across five products, the GitHub issue with 80 thumbs-up that has been open for two years.

## Step 1: gather inputs

Always start with AskUserQuestion (or natural-language parsing if the user already provided everything in one shot). Do not silently assume defaults; surface them. Capture, in this rough order:

1. **Domínio / theme.** What technology, product category, platform constraint, or market defines the search. The user states this freely (examples: "Meta no-display AI glasses", "B2B fintech for SMBs", "voice AI devtools", "Shopify apps for DTC brands", "ferramentas para creators no TikTok"). Do not constrain.
2. **Escopo geográfico.** Global, a specific country list, or exclusions ("global only, exclude Brazil", "US + Canada", "Europa + UK"). Take the user literally. If they say "global, no Brazil", strip Brazil-only signals and Portuguese-language-only plays from the output unless they also work globally.
3. **Perfil do builder.** Solo founder, two-person team, agency with capacity, funded team. Defaults to solo builder if unstated.
4. **Horizonte de lucro.** 3-6 months, 6-12 months, 12+ months. Defaults to 3-6 months.
5. **Tamanho do batch.** How many ideas per fire (default 5 to 10, never more than 10 unless user insists; fewer is fine if signal is thin).
6. **Caminho do arquivo .md.** Absolute path to the markdown file that accumulates ideas. Create it if it does not exist. Suggest a sensible default inside the user's workspace folder if they want one picked.
7. **Scope filter rules.** Any hard constraints: "must work with X", "must not depend on Y", "allowed shapes only Z", "no in-lens UI", "no auth via OAuth", etc. These become hard filters; anything that fails is dropped before drafting.
8. **Custom sources.** Optional domain-specific places to look (specific subreddits, developer forums, niche newsletters, industry trade publications, marketplace review pages). Added on top of the default source list.

If the user dumped all of this up front in natural language (as they often do when invoking the skill with a long prompt), parse what is there and only ask about the gaps.

## Step 2: read the existing file

Read the output file end to end. Extract every existing idea title and the pain/buyer pair behind each. Never produce a duplicate or a near-duplicate (same pain + same buyer counts as a duplicate even with different surface wording). Note the highest existing number; new entries continue from there.

If the file does not exist, create it. Open with a small header that records the parameters captured in Step 1 (domain, scope, builder profile, horizon, scope filter), so future invocations and the user can both see the constitution of the list.

## Step 3: pull fresh signal

WebSearch the last 30 days, with priority on the last 7 (gap signal accumulates more slowly than launch signal — a feature request that lives on for a month is stronger evidence than a one-day spike). Cite a specific source URL with a date per idea. Default source rotation, adapted to the domain:

- **Reddit unmet-request threads.** Hunt phrases like "is there a tool for", "what do you use for", "I wish someone built", "why isn't there a", "anyone know a good X" in domain-relevant subreddits plus the generic ones (r/SideProject, r/SaaS, r/Entrepreneur, r/smallbusiness). Strong signal: threads with substantial upvotes/comments where the answers are either silence, "I want one too", or a list of inadequate workarounds.
- **1-star and 2-star review patterns.** App Store, Google Play, Chrome Web Store, Shopify App Store, Slack App Directory, Notion integrations marketplace, VS Code marketplace, G2, Capterra, TrustPilot. The signal is a *recurring complaint* across multiple reviews of multiple products in the same category — that names a gap that no incumbent has closed.
- **GitHub feature-request issues.** Filter by reactions (>= 20 thumbs-up) or comment count (>= 30) with no merged PR and no maintainer commitment to ship. Old issues left open for years on popular repos are especially strong signal — the maintainers have decided not to do it, and a third-party tool can.
- **Stack Overflow / Stack Exchange unanswered popular questions.** Questions with high view counts (10k+) and no accepted answer in the domain. "How do I X?" threads where every answer is a workaround with caveats are gap evidence.
- **Hacker News "Ask HN: is there a..." threads.** Look at threads where the top answers are "no, but I want one too" or "I built a half-thing for myself and it works for me only".
- **IndieHackers / r/SaaS "anyone need" / "would you pay for" / "looking for" threads.** Skip revenue milestone posts in this mode — those are launched products. Focus on the threads where someone is describing an unmet need.
- **Product Hunt comments on recent launches.** Specifically the critiques: "this is cool but it doesn't do X", "why no Y?", "I'd pay for this if it worked with Z". Comments under launches are richer gap signal than the launches themselves.
- **Domain-specific forums and Q&A.** Depends on theme — devforums for a dev platform, accounting subreddits for vertical SaaS, designer Discords for design tools, etc. Read what people are *asking for*, not what's being launched.
- **Twitter/X complaint threads.** Search `"<product name>" is` filter:replies and skim for "frustrated", "broken", "missing", "I wish", "why doesn't this". One angry thread with 50+ "same here" replies names a gap.
- **Google Trends rising queries with a thin SERP.** Search slopes climbing where the top results are only blog posts and Reddit threads (no dedicated product page). Demand exists, supply does not.

Plus any custom sources from Step 1.

**Sources to deprioritize in this mode** (they surface hits, not gaps):
- Product Hunt top of the week launches
- IndieHackers "$X MRR" milestone posts
- Show HN posts about shipped products (the launch itself is not gap signal; the *gaps the comments call out* may be)
- Exploding Topics for products already on the rise

For every idea that survives Step 4, you must have at least one dated source URL from the last 30 days. If you cannot find one, the idea does not ship in this batch. This is the single most important rule of the skill.

## Step 4: apply scope filter

Drop, in this order:

1. Anything that violates the user's hard constraints from Step 1.
2. Anything you cannot back with a real, dated source URL.
3. Anything where a quick check reveals an existing product already solves the gap well at the named buyer's price point. (A product that *partially* solves it, or solves it for the wrong segment, leaves the gap intact — keep those, and name the inadequacy in `Existing workaround`.)
4. Anything that duplicates or near-duplicates an existing entry in the file.
5. Anything that clusters into the same vertical as several others in this batch (require at least 3 distinct verticals per fire to avoid a one-note batch).
6. Anything pitched at a geography the user excluded.

Drops are not failures; they are how the skill stays honest. If filtering leaves fewer than 5 ideas, ship fewer; report what you dropped and why.

## Step 5: write the ideas

Use this exact template per idea. All fields required.

```
### N. <short, specific title — name the gap, not a product>

- **Pain:** one or two sentences, concrete and personal — what someone is trying to do and can't, or is doing with friction
- **Existing workaround:** what people do today. Forms: "nothing — they live with it", "ductape across N tools", "a manual process that takes X hours", or "<product> kind of does it but <specific inadequacy>: price / scope / complexity / missing feature / wrong buyer"
- **Why nobody built this:** the honest reason this gap has stayed open. Examples: niche too small for funded teams, requires domain knowledge most builders lack, technology just became viable, demand scattered across forums with no single buyer to sell to, regulatory complexity, no obvious distribution channel
- **Why now:** what changed in the last 6 to 12 months that makes this winnable now (technology, pricing threshold, regulation, audience size crossed, platform API shipped)
- **Buyer / ICP:** who pays, with role / segment / size when relevant
- **Killer risk:** the single thing most likely to kill this, named honestly (incumbent wakes up, technology shifts under you, demand fragments, distribution closes)
- **Distribution reality:** how this actually ships given the constraints from Step 1 (companion app on a store, web app, dev preview org, partner program, browser extension, marketplace app, "wait for GA")
- **Gap signal:** <source URL> | <YYYY-MM-DD> | one line of evidence in plain English describing both *that the pain is real* AND *that no satisfactory solution exists* (e.g., "180-comment r/X thread asking for a tool to do Y; top reply is 'I built one for myself in a weekend', no link, 50 'please share' replies"; or "47 1-star reviews across 6 Chrome extensions for X all citing 'breaks on sites with infinite scroll'")
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

- How many gaps were added, the new total in the file.
- Any sources that were unusually rich this round (e.g., "3 of 7 came from Chrome Web Store 1-star review patterns").
- Anything you dropped on the scope filter and why, one line per drop, only if it helps the user calibrate the filter. Drops for "an existing product already solves this well" are worth surfacing — they tell the user where the saturated zones are.
- A direct link to the file. If it lives inside the Cowork workspace folder, use a `computer://` link.

Do not paste the full ideas into chat. The file is the artifact; chat is a 4 to 6 line status.

## Stop conditions

Self-report and pause if any of these become true on an invocation:

- The file hits 100 distinct ideas. Suggest the user shift from collecting to executing (and consider running `/great-ideas validate` to surface the top entries).
- Two consecutive fires fail to produce 5 non-duplicate, in-scope, signal-backed gap entries. The domain may be saturated (every gap already has a product) or the scope filter too narrow; surface this and offer to widen scope, swap sources, or pivot domain.
- The user says stop.

## Anti-patterns

- **Treating a launched product as a gap.** A Show HN at 200 points, a $5k MRR milestone post, a Product Hunt top-of-week — these are hits, not gaps. The gap is whatever the comments under those launches are still asking for.
- **Fabricated signals.** If the source URL is fictional, the gap is fictional. Drop it.
- **Hand-waving evidence** ("people seem to want this"). A gap entry needs a dated URL and a concrete count: number of upvotes, number of "same here" replies, number of 1-star reviews citing the same missing feature, view count on the unanswered question, reactions on the open issue.
- **Restating the same gap in different verticals to pad the count.** Same pain + same buyer is a duplicate even with a fresh title.
- **Pitching solutions for the excluded geography.** If the user said "global, no Brazil", do not slip in Brazil-only signals.
- **Mixing in-scope and out-of-scope shapes.** If the user said "no in-lens display", do not sneak in display-dependent ideas because they sound cool.
- **Skipping the "existing product check" in Step 4.** If a quick search shows a $9/mo SaaS already nails this for the named buyer, the gap is closed — drop the entry. The whole point of this mode is to find what's *not* there.
- **Long preamble in chat.** The file is the output; chat is a tight status.
- **Generic gaps that could have been written without WebSearch.** If the entry could exist regardless of what was posted in the last 30 days, it is not a fire-worthy gap.

## Examples of good vs bad gap signals

**Good:** `https://www.reddit.com/r/Notion/comments/.../is-there-a-way-to-bulk-export-notion-pages-as-clean-markdown | 2026-05-12 | 220-upvote thread asking for a tool to bulk-export Notion to clean markdown for use with static site generators. Top replies are "I wrote a janky python script", "I tried <tool> but it mangles toggles", and "would pay $10/mo for this". 110 comments, no link to a shipped solution.`

**Bad:** `Various sources | last week | People on Reddit seem frustrated with Notion export.`

The good one shows the pain (bulk export to clean markdown), names a buyer (people running static sites from Notion), shows the current workaround is inadequate (janky scripts, broken tools), and has a price anchor in the thread. The bad one is unfalsifiable filler.

## Optional: schedule a recurring fire

If the user wants the skill to fire on a cadence (every 2 hours, daily, weekly), do not build that into this skill. Generate the first batch, then offer to create a scheduled task that re-invokes the skill with the same parameters. The scheduling system is a separate tool; keep this skill focused on a single high-quality batch per invocation.
