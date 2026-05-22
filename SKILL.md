---
name: great-ideas
description: Use this skill whenever the user wants signal-backed build opportunities, side project ideas, startup ideas, validated product opportunities, or asks "what should I build" / "find me opportunities" / "ideias de negócio" / "ideias para empreender" for a specific market, sector, technology platform, or country. Triggers on phrases like /great-ideas, "fire de ideias", "lote de ideias", "great ideas para o ramo X", "opportunity scan em Y", "research signal no mercado Z", "ideias para builder solo", or any request that combines a domain (AI glasses, fintech, devtools, healthtech, voice AI, shopify apps, etc) with a desire to discover concrete things to build. Use this skill even when the user does not say the word "skill" but is clearly asking for a curated batch of opportunities pulled from live web signal (Product Hunt, Reddit, Hacker News, IndieHackers, App Store charts, Google Trends, developer forums). Prefer this skill over generic brainstorming whenever the request implies real-world validation, fresh signal, or a running list maintained in a markdown file. Also handles `/great-ideas validate [N]` (or "ranquear ideias", "top X das ideias", "validar a lista"), which reads an existing ideas file and ranks its entries into S/A/B/C tiers, picking the top N without any new WebSearch. Also handles `/great-ideas refire N` (or "preenche até N", "fill until N", "burst N ideias", "bulk fire", "rode até ter X ideias"), which runs multiple fire rounds back-to-back until the file reaches N total ideas, rotating sources per round and stopping early when signal is exhausted.
---

# great-ideas

Three modes. Pick one per invocation, then load the matching reference file and follow it end-to-end.

## When to use

- **fire** (default) — generate a fresh batch of signal-backed opportunities for a domain/market the user names, and append them to a running markdown file. Pulls live web signal (last 7 days), applies a scope filter, never fabricates. One batch per invocation.
- **validate** — read an ideas file already populated by previous fires and rank its entries into S/A/B/C tiers, writing a `## Top N` section at the top of the same file. Does **not** WebSearch; only judges what is already documented.
- **refire** — run multiple fire rounds back-to-back until the file reaches a target count N, rotating source emphasis per round and deduping aggressively. For when the user wants a burst ("fill to 50 now") instead of trickling fires manually or scheduling via `/loop`.

## Dispatch

Parse the invocation. Decide by the first significant token after `/great-ideas`:

1. First token is `validate` (e.g., `/great-ideas validate`, `/great-ideas validate 5`, `/great-ideas validate 15 /path/file.md`, `/great-ideas validate --top=20 --file=...`), or the natural-language equivalent ("ranquear minhas ideias", "rank the top 10 in /path/file.md", "validate the list") → load `references/validate.md` and follow it.
2. First token is `refire` (e.g., `/great-ideas refire 50`, `/great-ideas refire 30 ~/ideas/voice.md`, `/great-ideas refire --target=50 --file=...`), or the natural-language equivalent ("preenche até 50", "fill until 30", "burst 40 ideias", "rode até ter 100 no arquivo") → load `references/refire.md` and follow it.
3. Otherwise (the user named a domain, a market, a platform, or simply invoked `/great-ideas` with no further hint) → load `references/fire.md` and follow it.

If the invocation is genuinely ambiguous between modes, ask once with AskUserQuestion.

## Shared invariants (all modes)

- The output file is the artifact. The chat is a tight 4–6 line status (refire can stretch to 6–8). Never paste the full ideas or full ranking into chat.
- All written content in the output file is in English, regardless of the language the user used to invoke the skill.
- Slop is worse than silence. Two strong, in-scope, signal-backed entries beat eight filler ones. If real signal does not exist for an idea, drop it; never fabricate.
- Each mode owns its own stop conditions and anti-patterns — read them in the reference file.
