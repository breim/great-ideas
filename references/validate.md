# great-ideas — validate mode

Read an ideas file already populated by previous fires and rank its entries into S/A/B/C tiers, picking the top N. Write a `## Top N` section at the very top of the same file (replaces any prior one on each run). No WebSearch — validate only judges what is already documented in the file. If the user wants fresh signal, they should run a new fire.

The non-negotiable rule: tiers are evidence-based. An idea cannot be S if its `Trend signal` is hand-wave, even if every other field is strong. Validation depth is a veto, not a weight.

## Step 0: parse the invocation

Accept any of these shapes:

- `/great-ideas validate` → top **10**, file path inferred from the most recently used ideas file in this conversation; otherwise ask via AskUserQuestion.
- `/great-ideas validate <N>` → top N from the inferred or asked-for file.
- `/great-ideas validate <N> <path>` → top N from the explicit path.
- `/great-ideas validate --top=<N> --file=<path>` → same as above, in flag form.
- Natural-language equivalents in PT or EN ("ranqueia minhas 5 melhores ideias do arquivo X", "rank the top 20 in ~/ideas.md") count too.

N must be a positive integer. If N is missing, default 10. If the file path is missing and cannot be inferred, ask once.

## Step 1: read the file

Read the file end to end. Extract:

- The header block (domain, scope, builder profile, horizon, scope filter rules). This is what the builder profile reference is for in the rubric below.
- Every numbered idea (`### N. <title>`) and all its fields: Pain, Why now, Buyer / ICP, Validation signal, Killer risk, Edge, Distribution reality, Trend signal, plus the optional Modality line.
- The footer line.

If a previous `## Top N — validated ...` section is at the top of the file, ignore it during extraction (it will be replaced).

**Edge cases:**

- File missing or empty, or zero numbered ideas → stop. Report in chat: "0 ideas in the file. Run `/great-ideas` first to populate." Do not write to the file.
- File has fewer ideas than N → proceed anyway, ranking all of them. Note the shortfall in the chat report ("asked for top 10, only 6 ideas in the file — ranked all 6").
- An idea has missing fields → do not crash. Treat each missing field as a weak signal on the corresponding rubric dimension below.

## Step 2: assign a tier per idea

Apply this qualitative rubric to every idea. Five dimensions, each judged from the field in column 2. The tier rules below combine the dimension results — there is no numeric score, do not invent one.

| Dimension | Read from | Strong | Medium | Weak |
|---|---|---|---|---|
| **Validation depth** | `Trend signal` | Dated URL within last ~30 days + concrete number (MRR, ARR, >50 upvotes, clear Google Trends slope, named waitlist size) | Dated URL but qualitative evidence only, or older (30–90 days) | Hand-wave, undated, missing URL, or generic "people on Reddit want this" |
| **Buyer clarity** | `Buyer / ICP` | Role + segment + size named (e.g., "ops lead at 50–200 person DTC brands") | Role or segment named but not both | Generic "people who want X", "consumers", "businesses" |
| **Time-to-ship** | `Distribution reality` | Shippable this week with no platform/partner dependency | Needs a platform feature already in beta, or a non-blocking partnership | Waits on platform GA, regulatory approval, exclusive partner contract |
| **Killer risk fatality** | `Killer risk` | Manageable risk with a clear mitigation | Meaningful risk but a workaround exists | Likely-fatal: incumbent moat, legal blocker, unit economics broken |
| **Edge fit** | `Edge` compared against the builder profile in the file's header | The named builder profile clearly wins vs. an incumbent or funded team | Arguable edge but not a structural one | Hard to defend; a funded team would beat this builder |

Tier rules — apply in this order, first match wins:

- **C — Drop**: validation depth is weak, **or** killer risk is weak (likely-fatal), **or** the idea duplicates a stronger entry already tiered S/A on the same pain+buyer pair.
- **S — Ship now**: validation depth strong **and** buyer clarity strong **and** time-to-ship strong **and** killer risk at least medium **and** edge fit at least medium.
- **A — Ship soon**: validation depth strong **and** buyer clarity strong, but exactly one of {time-to-ship, killer risk, edge fit} is medium-or-weak (not both weak).
- **B — Watch**: everything else that isn't C.

Validation depth is a veto for S. An idea with weak validation depth cannot be S, full stop, even if every other dimension is strong.

## Step 3: pick the top N

Order: all S → all A → all B → all C. Within a tier, break ties by validation depth (more recent URL + bigger number wins). Take the first N.

If the file has fewer than N ideas total, take all of them. If more than N ideas are tied at the bottom tier needed to fill the slot, take the highest-validation ones and note in the chat that there was a tie.

## Step 4: write the section

Insert (or replace, if one exists) at the very top of the file, immediately below the parameter header block and immediately above the first idea (`### 1. ...`). Use this exact format:

```markdown
## Top N — validated <ISO timestamp>

> Ranked from the entries below using the validate rubric. Re-run `/great-ideas validate N` to refresh. No WebSearch — reads the file only.

1. **#<orig-num> — <original title>** `[S]` — <one line: why this beats the rest>
2. **#<orig-num> — <original title>** `[S]` — <one line>
3. **#<orig-num> — <original title>** `[A]` — <one line + the single thing to watch>
...
N. **#<orig-num> — <original title>** `[B]` — <one line>

**Tier distribution (all M ideas in the file):** S: x · A: y · B: z · C: w
**Drop notes (optional):** "#4 and #9 fell to C — duplicate buyer of #2 and #7."
```

Use `date -u +%Y-%m-%dT%H:%M:%SZ` if the shell is available; otherwise use the current ISO date from context.

Detection of an existing section to replace: heading line starts with `## Top ` and is followed within 5 lines by `> Ranked from the entries below`. Replace the whole block from the heading through the `**Drop notes**` line (or `**Tier distribution**` line if drop notes were omitted), inclusive. Do not stack historical Top N sections.

The one-line reason per entry must be **specific** — point at the actual differentiator (a number from the Trend signal, a concrete buyer detail, a named killer risk). Avoid generic phrases like "strong signal" or "good buyer fit".

## Step 5: update the footer

Update the footer line at the bottom of the file. Format:

```
Total ideas: N | Last fire: <iso> | Last validate: <iso> | Domain: ... | Scope: ... | Builder: ... | Horizon: ...
```

If the existing footer has no `Last validate:` field, insert it immediately after `Last fire:`. If it already has one, overwrite the timestamp. Preserve `Last fire:` and all other fields verbatim.

## Step 6: report to the user

Chat status, 4–6 lines, no more:

- One line: "Validated M ideas. Top N written at the top of `<path>`."
- One line: tier distribution (e.g., "S: 2 · A: 5 · B: 8 · C: 3").
- One or two lines: the top pick and, if interesting, one surprise (e.g., "#7 jumped to S despite being older — revenue post landed last week").
- Optional one line: most interesting drop (only if it helps the user calibrate the filter).
- Link/path to the file.

Do not paste the ranking into chat. The file is the artifact.

## Stop conditions

- Empty file or zero numbered ideas → stop with a message, do not write anything.
- More than 70% of ideas fall to C → do not write the section. Report in chat: "70%+ of ideas are C-tier — the filter is too tight, the domain is exhausted, or fires are producing slop. Consider widening scope, swapping sources, or pivoting domain before running validate again."
- The user says stop.

## Anti-patterns

- Inventing a numeric score (e.g., "8.4/10"). This skill is tier-based on purpose; numeric scores imply a precision the rubric does not have.
- Re-fetching URLs or running new WebSearch. Validate is offline-by-design; if the user wants fresh signal they should fire again.
- Tiering as S anything whose `Trend signal` is hand-wave. Validation depth is a veto.
- Stacking historical Top N sections in the file. Always replace, never append.
- Re-writing the original ideas, their numbers, their fields, or anything below the Top N section. Validate is read-only on existing entries.
- Generic one-liners ("good fit", "strong signal", "worth building"). Each line must point at a specific differentiator.
- Pasting the ranking into chat instead of writing it to the file.
