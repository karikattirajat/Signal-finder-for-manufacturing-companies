---
name: signal-finder-for-manufacturing-companies
description: Bulk buying-signal research for manufacturing and consumer-goods account lists. For each company in a CSV, finds ONE recent, verifiable industry, regulatory or geopolitical signal that creates a business challenge the seller can solve, and compresses it into a ~30-word cold-email hook with sources. Use when the user hands over a list of manufacturers and wants "signals", "hooks", "trigger events", "why-now" openers or ABM research per account.
---

# Signal Finder for Manufacturing Companies

## Objective
For each company in `input/companies.csv`, find ONE recent, verifiable signal that creates a
business challenge the seller can solve, and compress it into a ~30-word one-liner usable as a
cold-email hook. Write results to `output/signals.csv` incrementally.

## Before you start
1. Read `config/seller_context.md` (copy it from `seller_context.example.md` if missing and ask
   the user to fill it in). It defines: what is sold, who buys it, the "solvable" capability
   list, the target country, the signal window, and house-style banned words.
2. Read `input/companies.csv` (format: `templates/companies_template.csv`).
3. Read `output/signals.csv` if it exists and SKIP companies already processed (resume).

## Hard rules for every signal
1. **Time window.** The signal must create a challenge that is active or intensifying inside the
   signal window from the seller context. A signal that peaked and resolved earlier is REJECTED.
   A policy announced earlier whose compliance or margin impact lands inside the window is ACCEPTED.
   Always state why it is live in the window.
2. **Country-applicable.** Global trends count only if they demonstrably hit the target country's
   operation. A trend affecting only another region of the business is REJECTED.
3. **Pan-national by default.** Use a state/region-specific signal ONLY if it is the company's
   home region or dominant market, and flag it so the user can decide per recipient.
4. **Verifiable and recent.** Every signal needs at least one source URL dated within the last
   12 months (prefer 6). No estimates, no fabricated figures, no undated claims. If it cannot be
   verified, write `NO_VERIFIED_SIGNAL` instead of guessing.
5. **Solvable.** The implication must connect to the seller's capability list. If the challenge
   is purely R&D, financing or capacity with no link to the seller's capabilities, find a
   different signal or mark `WEAK_FIT`.
6. **Channel caveat.** If the company sells mainly B2B or through project/institutional channels
   and the seller's product targets dealer/general-trade channels, mark `WEAK_FIT` and still find
   the best channel-angle signal available.

## Signal categories (priority order)
1. Tax / regulatory: tax-slab changes, excise policy, price caps, quality-control orders,
   production-linked incentive schemes, anti-dumping duties
2. Input-cost volatility: crude-linked resins, packaging inflation, commodity prices, import dependence
3. Trade / geopolitical: tariffs on exports, supply dependence on one country, trade agreements,
   shipping-route disruption
4. Demand-structure shifts: premiumisation, new sales channels (e.g. quick commerce), rural vs
   urban divergence, category consolidation
5. Competitive / channel: unorganised-sector competition, dealer credit stress, share consolidation

## Search method per company
1. Identify industry, sub-category, HQ state, channel model (from the CSV first, web search if missing).
2. Run 1-3 targeted web searches, using the current date in queries:
   `[industry] <country> <year> challenges`, `[industry] <country> regulation / policy <year>`,
   `[company] <country> <year>`.
3. Pick the single strongest signal by: verifiability > recency > company-specificity > seller fit.
4. Write the one-liner: trend + implication, max 30 words, **problem-led** (lead with the
   prospect's problem, not the product), no product pitch inside it. Avoid the banned words in
   the seller context.

## Output schema: `output/signals.csv`
| column | content |
|---|---|
| company | Company name as given in input |
| industry | Category identified |
| channel_model | general_trade / dealer_network / b2b_institutional / mixed |
| signal | The trend or event in one sentence |
| signal_date | Month/date of the source event or article |
| source_url | Primary source URL |
| source_url_2 | Optional second source |
| why_live_in_window | One sentence on why this bites inside the signal window |
| scope | pan_national / state_specific:[state] |
| one_liner | The <=30-word hook (trend + implication) |
| seller_angle | Which seller capability maps to the implication (one phrase) |
| fit_flag | STRONG / WEAK_FIT / NO_VERIFIED_SIGNAL |

## Operational rules for the batch run
- Process in batches of 20-25 companies. **Append to `output/signals.csv` after every batch** so
  progress survives interruption.
- Companies in the same industry share industry-level research: research the industry once, cache
  it in `output/industry_signals.md`, reuse it (re-verify the date/URL is still live), but still
  re-derive the one-liner per company for its scale, geography and portfolio.
- Maintain `output/run_log.md`: batch number, timestamp, companies done, companies flagged
  `NO_VERIFIED_SIGNAL` or `WEAK_FIT`.
- Do not invent metrics. Do not cite seller case-study numbers in the one-liner.
- At the end write `output/summary.md`: count by fit_flag, count by industry, and every
  state-specific signal for manual review.
