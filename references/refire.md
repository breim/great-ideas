# great-ideas — refire mode

Run multiple fire rounds back-to-back against the same file until it reaches a target count N, rotating source emphasis per round to maximize coverage of the gap-signal surface across forums, marketplaces, and issue trackers. Stop early when the target is hit, when signal is exhausted, or when the file hits the global 100-idea ceiling. Refire is a burst, not a schedule — for scheduled mining over time, the user should use Claude Code's `/loop` against `/great-ideas` directly.

The non-negotiable rule: refire does not lower the quality bar to hit N. If a round can't produce 5 strong gap entries (real, dated, unsolved), that's a signal that the domain is saturated at this moment — stop and report, do not pad.

## Step 0: parse the invocation

Accept any of these shapes:

- `/great-ideas refire <N>` → target N total ideas in the file. File path inferred from the most recently used ideas file in this conversation; otherwise ask once via AskUserQuestion.
- `/great-ideas refire <N> <path>` → target N from the explicit path.
- `/great-ideas refire --target=<N> --file=<path>` → flag form.
- Natural-language equivalents in PT or EN ("preenche o ~/ideas/voice.md até 50", "burst 30 ideas into ideas.md") count too.

N must be a positive integer ≥ 1 + current count. If N is missing, ask. The file path is required and cannot be inferred to a fresh file — refire builds on existing context (see Step 1).

## Step 1: read the file and validate preconditions

Read the file end to end. Extract:

- The parameter header block (domain, scope, builder profile, horizon, scope filter rules, custom sources if any). This is the search context that every round will reuse.
- The count of existing numbered ideas (M).
- The pain+buyer pairs already represented (for cross-round dedup tracking).

Preconditions, checked in order:

1. **File missing or has no parameter header** → stop. Report: "Refire needs an existing file with a parameter header. Run `/great-ideas` first to seed it (the skill will capture domain/scope/builder profile/etc), then refire to fill to <N>."
2. **M ≥ N already** → stop. Report: "File already has M ideas, target was N. Nothing to refire. Run `/great-ideas validate` to surface the top entries."
3. **M ≥ 100** → stop. The global ceiling from fire mode has been hit. Report: "File is at the 100-idea ceiling. Shift from collecting to executing — run `/great-ideas validate` to pick what to build."

If preconditions pass, proceed.

## Step 2: the refire loop

Initialize:

- `target = N`
- `current = M`
- `rounds = 0`
- `consecutive_thin = 0` (rounds that added fewer than 5 non-duplicate signal-backed ideas)
- `max_rounds = 10` (hard safety cap)

Loop while **all** of these hold:

- `current < target`
- `current < 100`
- `consecutive_thin < 2`
- `rounds < max_rounds`

Each iteration:

1. Run a single fire round by following `references/fire.md` Steps 2 through 6. Skip Step 1 (input gathering) — the parameter header already in the file is the input, do not re-ask.
2. Apply the source emphasis for this round number (see table below) on top of the standard fire source list. The emphasis biases where the round starts looking; it does not exclude other sources.
3. Enforce strict dedup against **every** existing idea in the file, including ones added by earlier rounds of this refire (same pain+buyer pair = duplicate, drop it).
4. After the round, recount total ideas in the file. Let `added = current' - current`.
5. If `added < 5`, increment `consecutive_thin`. Otherwise reset `consecutive_thin = 0`.
6. Set `current = current'`. Increment `rounds`.

### Source emphasis per round

| Round | Emphasis |
|---|---|
| 1 | Reddit unmet-request threads ("is there a tool for", "what do you use for", "I wish someone built") in r/SideProject, r/SaaS, r/Entrepreneur + domain-specific subreddits |
| 2 | 1-star and 2-star review patterns on marketplaces in scope (App Store, Google Play, Chrome Web Store, Shopify App Store, G2, Capterra) — recurring complaints across multiple products in the category |
| 3 | GitHub feature-request issues with high reactions and no merged PR + Stack Overflow / Stack Exchange questions with high views and no accepted answer in the domain |
| 4 | Hacker News "Ask HN: is there a..." threads + IndieHackers "anyone need" / "looking for" threads + Product Hunt comments asking for what recent launches don't do |
| 5+ | Cycle back to the round-1 sources but bias toward whichever sources produced the highest-quality gap entries in earlier rounds of this refire |

The standard fire batch-level rules still apply per round: at least 3 distinct verticals, dated source URLs, scope filter enforced (including the "existing product already solves this well" check), numbering continued from the highest existing entry. Refire does not relax any of these.

## Step 3: terminate

The loop exits because one of the conditions became false. Categorize the exit reason:

- `current >= target` → **target hit**
- `current >= 100` → **ceiling hit**
- `consecutive_thin >= 2` → **signal exhausted**
- `rounds >= max_rounds` (10) → **max rounds**

The footer of the file naturally reflects the final state — each round's Step 6 updated `Total ideas` and `Last fire`. No additional footer write needed.

## Step 4: report to the user

Chat status, 6–8 lines (refire is bigger than a single fire, so a bit more room than usual):

- One line: "Refire complete. File went from M → <current> ideas across <rounds> rounds (target was <target>)."
- One line: exit reason in plain English.
  - Target hit: "Target reached."
  - Ceiling hit: "Hit the 100-idea ceiling — stopping. Run `/great-ideas validate` next."
  - Signal exhausted: "Two consecutive rounds added fewer than 5 strong gap entries. Domain is saturated for now (most gaps already have products, or the scope is too narrow) — consider widening scope, swapping sources, or coming back in a week when fresh complaints land."
  - Max rounds: "Hit the 10-round safety cap without reaching target. The domain doesn't have enough live signal to support N ideas right now."
- One or two lines: per-round adds, only if the variance is interesting (e.g., "round 2 added 9, round 4 added 3 — IndieHackers carried this refire").
- One line: "Run `/great-ideas validate <N>` to surface the top entries now that the file is bigger."
- Link/path to the file.

Do not paste new ideas into chat. The file is the artifact.

## Stop conditions specific to refire

- Any of the loop exit conditions above.
- User says stop (refire can be interrupted between rounds; do not interrupt a round mid-flight).

## Anti-patterns specific to refire

- **Padding to hit N.** If a round can't produce 5 strong gap entries, report thin signal and let the loop terminate. Never relax the dedup rule, the dated-URL rule, the "existing product check", or the scope filter to hit a number.
- **Reusing source URLs across rounds.** Each round must surface different threads, reviews, issues — that's what the source emphasis rotation is for.
- **Skipping the parameter header.** Refire reuses the existing header verbatim; do not re-prompt the user for domain/scope/etc, and do not modify the header.
- **Running refire on a fresh file with no header.** Redirect to `/great-ideas` first.
- **Calling validate automatically at the end.** Refire suggests validate in the report, but the user invokes it explicitly — refire and validate are separate modes by design.
- **Updating the footer manually.** Each round's Step 6 (in `fire.md`) handles the footer; refire does not touch it directly.
