# Signal Finder for Manufacturing Companies

A [Claude Code](https://claude.com/claude-code) **skill** for account-based outbound. Give it a
CSV of manufacturing / consumer-goods companies and it researches the web to find, for each one,
**one recent, verifiable "why now" signal** (a tax change, input-cost spike, tariff, channel shift,
policy deadline...) and turns it into a **~30-word, problem-led email hook with sources**.

It does not guess. If a signal can't be verified with a dated source, the row is marked
`NO_VERIFIED_SIGNAL` instead of being invented.

## What you get per company
`industry`, `channel_model`, the `signal`, `signal_date`, `source_url`, why it is live in your
chosen time window, `scope` (nationwide vs state-specific), the `one_liner` hook, which of *your*
capabilities it maps to, and a `fit_flag` (STRONG / WEAK_FIT / NO_VERIFIED_SIGNAL).

## How it works
1. **Configure once.** Describe what you sell, who buys it, what problems it solves, the target
   country and the time window in `config/seller_context.md`.
2. **Load accounts.** Put your list in `input/companies.csv` (see `templates/companies_template.csv`).
3. **Research per company.** Claude identifies industry, region and channel model, then runs
   targeted searches in priority order: regulatory/tax, input costs, trade/geopolitics, demand
   shifts, competitive/channel pressure.
4. **Pick the strongest signal** by verifiability > recency > company-specificity > fit to what you sell.
5. **Write the hook:** trend + implication, max 30 words, problem-led, no product pitch.
6. **Save progressively** in batches of 20-25, so interrupted runs resume where they stopped.
7. **Reuse industry research.** Companies in the same industry share cached findings, but each
   hook is re-tailored to the company.
8. **Wrap up** with a run log and a summary: counts by fit flag and industry, plus every
   state-specific signal to review manually.

## Quality rules built in
Signals must be live in your time window, apply to your target country, carry a source dated within
the last 12 months, and connect to something you can actually solve. Fabricated figures and
undated claims are rejected.

## Setup
```bash
git clone https://github.com/karikattirajat/Signal-finder-for-manufacturing-companies.git
mkdir -p ~/.claude/skills
cp -R Signal-finder-for-manufacturing-companies/signal-finder-for-manufacturing-companies ~/.claude/skills/
cd ~/.claude/skills/signal-finder-for-manufacturing-companies
cp config/seller_context.example.md config/seller_context.md   # then fill it in
cp templates/companies_template.csv input/companies.csv        # then add your accounts
```
Restart Claude Code and ask: *"Run the signal finder on my companies list."*

## Privacy
No API keys, credentials, client names or prospect data are in this repository. `input/`,
`output/` and `config/seller_context.md` are git-ignored so your account lists and research stay
local. Web research uses Claude Code's built-in search; no keys are required.

## Layout
```
signal-finder-for-manufacturing-companies/
  SKILL.md                         The workflow Claude follows
  config/seller_context.example.md Your product, buyers, window, house style
  templates/companies_template.csv Input format
  input/  output/                  Your data (git-ignored)
```
