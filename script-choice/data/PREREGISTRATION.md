# Pre-registration — is Japanese kanji/kana choice cost-efficient?

Frozen 2026-10-31, before any observed-vs-null statistic of this project was computed. The freeze is the SHA-256 of this file, stamped in the project log and checked by `src/analyze.py` before it runs. Departures go in `REPORT.md` § Deviations; this file is never edited.

Known before freezing: q37's results on cost C1 (obs/null 1.19 strict token, 1.22 strict row, 1.01 broad token, 1.16 broad row), and the structure checks in `out/build_report.md` (row grain, form-count validation, the register files' layout, character coverage). No other outcome was looked at.

## Data

- BCCWJ 書字形一覧 ver 1.1, pooled (`BCCWJ_WritingFormTable.xlsx`) and by register (`…_byRegister_Pt1.xlsx` + `_Pt2.xlsx`), SHA-256 in `out/build_report.md`. The register parts split the rows between them, and per-register totals sum exactly to the pooled 合計頻度 for every row, so their union is a 13-way register partition.
- **Row** = one (語彙素ID, 語彙素読み, 品詞) record: 190,369 rows (verbs keyed by 語形, per the table's own header).
- **Choice set** of a row = its attested non-empty writing forms; p(form) = form frequency / 合計頻度.
- **Script class** per form, as q37: kanji (has a KANJIDIC2 character, no kana), hiragana, katakana, mixed (kanji + kana), other.
- **Universes.** *Strict*: rows with ≥1 kanji form and ≥1 hiragana-or-katakana form. *Broad*: rows with ≥1 kanji-or-mixed form and ≥1 hiragana-or-katakana form. Strict is primary.

## Costs (fixed set; a form's cost is the sum over its non-space characters)

| Id | Name | Per-character cost | What it stands for |
|---|---|---|---|
| C1 | q37 cost | KANJIDIC2 strokes for kanji; 1 for any other character | Doc 12 §5.4's named cost; replication anchor |
| C2 | all strokes | KANJIDIC2 strokes for kanji; KanjiVG r20260714 strokes for kana and other characters it covers; 1 otherwise | Handwriting effort with kana charged fairly |
| C3 | characters | 1 | Length on the page / reading positions |
| C4 | visual complexity | perimetric complexity P²/(4πA) of the MS Gothic glyph at 128 px (ink = grey < 128; P = 4-neighbour ink/background edges; A = ink pixels) | Perceptual load for the reader |

**Which costs matter most.** BCCWJ is printed and typed text, so its writers never paid a stroke cost. The costs its texts actually impose fall on readers (C3, C4). C1 and C2 are kept for continuity with doc 12 and q37, and to model handwriting.

**Excluded, with reasons.** *Morae*: identical for every form of a row (same reading), so they cannot discriminate a choice; morae enter only as a stratifier if needed. *Keystrokes / IME conversion*: romaji input is identical across a row's forms; the remaining difference (conversion and candidate selection) needs IME logs this project does not have. *Reading-time norms* for orthographic variants: no source on disk. *Romaji forms*: none attested in the table.

## H1 — aggregate efficiency (primary)

For row i with costs c and probabilities p over k forms: obs_i = Σ p·c; null_i = mean(c) (the within-row uniform null, which equals the expected cost under random permutation of p across the row's forms); min_i = min(c).

- Statistic: R = Σ w_i·obs_i / Σ w_i·null_i, with weights w = 合計頻度 (*token*, primary) or 1 (*row*). Also reported: obs/min.
- Permutation test: permute each row's p vector across its forms, B = 2,000; two-sided p from |log R*| ≥ |log R|.
- Uncertainty: bootstrap over rows, B = 2,000, percentile 95% CI.
- RNG: `numpy.random.default_rng(20261031)`.
- Cells: 4 costs × 2 universes × 2 weightings = 16. **Primary cells: strict × token, one per cost (4 cells); Holm correction across these four.**
- **Verdict rule per cost (primary cell):** *efficient* if R < 1, Holm-adjusted p < 0.05 and the CI excludes 1; *inefficient* if R > 1 under the same conditions; otherwise *cost-neutral*.
- **H1b (modal form):** the share of rows whose most frequent form is a minimum-cost form (a tie for most frequent counts fractionally), against its null expectation, the mean over rows of (number of minimum-cost forms ÷ k). Row-weighted; strict and broad.
- **Replication gate (Type-A):** C1 must reproduce q37's universe sizes (11,915 strict / 19,451 broad) and ratios (1.19 / 1.22 / 1.01 / 1.16) to two decimals. If it fails, stop and report before reading any other cell.

## H2 — frequency conditions the choice (within-row brevity)

- Sample: strict rows with 合計頻度 ≥ 100 (low-count rows sit near the null by construction, since every attested form has ≥1 token). Rows where all forms cost the same under a cost are dropped for that cost.
- Per-row efficiency index e_i = (obs_i − null_i) / (max_i − min_i), in [−1, 1]; negative = cheaper than chance.
- Statistic: Spearman ρ between log10(合計頻度) and e, per cost, with a bootstrap 95% CI over rows (B = 2,000).
- Prediction if frequency drives efficiency: ρ < 0. **Supported** if the CI lies below 0; **robust** if ρ < 0 with the CI below 0 also within each of the 名詞 and 動詞 strata (major 品詞).
- Reported alongside: ρ per 語種 (和, 漢, 外, 混, 固) and per major POS, and a decile table of mean e by frequency. Sensitivity: thresholds 20 and 1,000; broad universe.
- Known confound (see PRIOR-ART.md): frequent verbs carry grammaticalised (auxiliary) uses that are written in kana by convention. The POS strata are the check; the confound cannot be removed with this table.

## H3 — register (descriptive)

- Row set: strict rows; the choice set stays the pooled one; p comes from the register's own counts (a pooled form unattested in the register has p = 0).
- Per register (13): R_r over rows with ≥10 tokens in that register, weights = pooled 合計頻度 (common weights) and = 1.
- Contrast: formal F = {OL 法律, OW 白書, PN 新聞} vs informal I = {OC 知恵袋, OY ブログ}. Tokens are pooled within each group; rows qualify with ≥10 tokens in both groups. Report Δ = R_I − R_F per cost, with a bootstrap 95% CI over rows (B = 2,000), token and row weights.
- Expectation, stated as such: formal registers use more kanji, so under the stroke and complexity costs (C1, C2, C4) Δ < 0, and under C3 Δ > 0. H3 locates registers relative to each cost; it **cannot** separate efficiency from orthographic convention (OL/OW/PN follow codified 表記 rules).

## Sensitivity (all reported, none decisive)

- S1: drop forms with < 1% of the row's tokens before testing (a row must still qualify for its universe).
- Broad universe and row weighting for every H1 cell; H2 thresholds as above.
- C4 is undefined for forms containing a character with no MS Gothic glyph (1 character); such rows are dropped from C4 cells only.

## Reporting

Every cell above is reported, whatever its sign. The report states each cost's verdict, H2 and H3 results with their limits, and any deviation from this file with its reason. A fresh-context reader that did not write the code reviews the report against the code and outputs before it is called done.
