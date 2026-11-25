# Errata

Corrections, newest first. A published row is never edited or deleted; if something is wrong it
is stated here, and the corrected value is added as a new row.

## 2026-11-25 — early picks were left out of this ledger

Since 1 November 2026 the website has published a second list of picks, the early picks, ahead of
the day's main board. None of them reached this repository: the upload only read the main board
and the legs backed from it. Nobody decided to leave them out. The upload missed them, and the
record here was incomplete because of it.

- **From 25 November 2026, 08:17 UTC, every early pick is committed before kickoff**, in the same
  way as the main board. Each kickoff date has its own commitment,
  `commitments/early/<date>.<batch>.sha256`. Its rows are released to `early.csv` in that date's
  week folder once the date's matches are final, and graded rows go to `early_settled.csv`.
  `tools/verify.py` checks every released batch against its hash.
- **The 103 early picks with a kickoff between 20 November and that moment are in `early.csv`
  with the others, marked `uncommitted` in the `batch` column.** The website published them but
  the upload missed them, so they were never committed here: 75 from leagues, 27 from cups and 1
  from women's football, kicking off from 20 November 17:00 UTC to 25 November 03:00 UTC. Their
  `posted_utc`, the time the website first listed each one, comes from our own system and nothing
  here proves it. They are included, winners and losers alike, because a record that dropped
  every pick it could not prove would be a selected record. `tools/verify.py` counts them with
  the other early picks and also shows them on their own.
- **The 154 early picks with a kickoff from 1 to 19 November are not added.** The website's
  current track record starts on 20 November 2026, and the early picks here start on the same
  date.
- If an early pick is ever listed too close to kickoff to be committed in time, it is added to
  `early.csv` the same way, marked `uncommitted`, once its match is over.

## 2026-09-27 — predictions from 25 to 27 Sep that were never committed

On 25 Sep the upload released the 24 Sep predictions and the three fixtures committed early for 25 Sep. It then
numbered that day's main pass as batch 1 again, which would have replaced the published
`commitments/2026-09-25.1.sha256`. A published line is never rewritten, so the upload refused to publish, as it is
built to, and every upload after it refused until the fault was fixed.

- `commitments/2026-09-25.1.sha256` never changed. The rows it covers are released and match it, as do the 24 Sep
  rows. Check with `tools/verify.py`.
- The 25 Sep main pass (22 fixtures, 6 picks), every fixture dated 26 Sep (119) and the first 9 of the 21 fixtures
  committed early for 27 Sep kicked off without a commitment. They are not part of this record and never will be. The
  other 12 are in `commitments/2026-09-27.1.sha256`.
- Batches are now numbered from the commitments already published, and an upload refuses to write a commitment that
  already exists.

## 2026-09-21 — every losing leg was recorded as a zero loss

**The `profit_1u` column in `settled.csv` is wrong on every losing row published so far, and the
record it produced was far too flattering.**

`settle_frac` is the fraction of a stake that **won** — 1.0 a clean win, 0.75 a quarter-line
half-win, 0.5 a push with the stake refunded, 0.25 a quarter half-loss, 0.0 a loss. The
publisher computed a loss as `-settle_frac`, and `settle_frac` is **zero** on a loss, so every
losing leg was booked as `-0.00` instead of `-1`. Winners were unaffected.

On the 34 legs settled in W38:

| | published | correct |
|---|---|---|
| net | **+7.88 units** | **−6.12 units** |
| return | **+23.88%** | **−18.55%** |

That is a 42 percentage-point error in the most load-bearing number in this repository. The hit
rate (19/34 = 55.9%) was never affected, and neither was any probability, price, CLV figure or
result — only the money column derived from them.

What has been done:

- **`profit_1u` is no longer published.** The net return is fully derivable from `odds`,
  `result` and `settle_frac`, so storing it added a place to be wrong and nothing else.
- **`tools/verify.py` now computes the return itself**, from the grade, for every row old and
  new. Run it and you get −18.55%, not the figure in the old column.
- The wrong values are **not edited out** of the rows already published, in line with this
  file's purpose. `profit_1u` in `2026/W38/settled.csv` should be ignored; recompute from
  `odds`, `result` and `settle_frac`, or just run `tools/verify.py`.
- `METHODOLOGY.md` also described `settle_frac` as "the fraction of the stake that settled",
  which is the same misreading that caused the defect. It now states the five values.

## 2026-09-20 — published files rewritten to remove data-source names

Every commit in this repository was rebuilt on this date. `odds_source`, the column naming the
bookmaker, exchange or data feed behind each price, was removed from `picks.csv` for both days
already published (2026-09-18 and 2026-09-19), and three fair-price columns were renamed.

This repository promises that a published line is never rewritten. That promise was broken here
deliberately, once, and it is recorded rather than done quietly:

- **Commit hashes before 2026-09-20 have changed.** An older clone will not fast-forward.
- **No probability, price or result was changed.** One column was removed, three renamed, seven
  added. Every surviving value was carried across verbatim, and that was checked rather than
  assumed: the fair prices were recomputed from the price ledger and all 203 published legs and
  all 34 settled legs reproduced their published values exactly.
- **The timestamp proofs in `stamps/` are kept as they were, not regenerated.** The
  2026-09-19 manifest still matches `board.csv`, `goals.csv`, `results.csv` and `scores.csv`
  byte for byte, so the anchored proof that every probability and every score pre-dated its
  kickoff survives this rewrite untouched. Only `picks.csv` and `settled.csv` no longer match
  it, which is this change. A fresh manifest for the rewritten tree is added alongside.
- The 2026-09-18 manifest already did not match the current files before this rewrite: those
  files grew on 2026-09-19, and a manifest fixes a file as it stood at that run. That is how an
  append-only file and a point-in-time hash interact, and it is not a consequence of this
  change.

The reason is licensing rather than presentation. What it does *not* achieve is worth being
straight about: `odds` is still the price actually taken at one venue. The venue's identity is
withheld; its number is not.

