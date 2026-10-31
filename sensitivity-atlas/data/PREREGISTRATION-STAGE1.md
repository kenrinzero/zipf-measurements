# Pre-registration — atlas stage 1: the core Zipf α table

Frozen 2026-10-31, before any matched-size fit of this project was computed. The freeze is this file's SHA-256, stamped in the project log and asserted by `src/fit.py`. Departures go in the stage 1 report's Deviations section; this file is never edited.

**Known before freezing:** every archived full-size fit listed under the regression gates below (they are the dossier's published numbers); the leg sizes in `out/legs.csv`; one timing fit that reproduced the archived Aozora surface value (α_rank 1.0163, xmin 1396). No matched-size statistic had been computed.

## Question

Doc 01 §5.3 of the zipf dossier ranks the analysis choices that move a Japanese word-level Zipf exponent: analyzer/dictionary (≤ 0.016) < register (±0.13) < 語種 stratum (±0.35) < unit. Later questions added that grain widens the analyzer band (q34), that the estimator/xmin choice outweighs register and stratum (q23), and that corpus, once xmin is controlled, is smaller than register (q23). Each number came from its own convention and corpus size. **Stage 1 asks whether that ordering survives when every leg is fitted with one estimator at one token count.**

## Legs (58; registry `src/legs.py`, sha256 `06bfee7a…b648`; sizes `out/legs.csv`, sha256 `f6100021…3b85`; input hashes `out/input_hashes.txt`, sha256 `3a85725b…be7b`)

- **AOZ-surf × 5 analyzers** (q02 lists: MeCab+UniDic-lite, MeCab+IPAdic, Sudachi A/B/C), cleaned Aozora, ~38.6M tokens.
- **AOZ-lem × 8 analyzers** (q34 lists: the five above at lemma grain, plus Sudachi A/B/C `normalized_form`).
- **BIB-surf** (q23 complete list) and **BIB-lem** (new, `src/build_bible_lemma.py`: q23's method with `feature.lemma`, falling back to surface as q34 did; its surface counts reproduce q23 exactly, 978,070 tokens / 16,008 types).
- **BCW-SUW** ALL + 13 registers (q07 lists, lemma grain, NINJAL SUW list v1.1).
- **BCW-LUW** ALL + 13 registers (q16 lists, LUW list v1.0).
- **BCW-SUW strata** 和 / 漢 / 外 / 混 / 固, cut from q07's ALL list by its 語種 column.
- **BCW-writtenform** (new): BCCWJ 書字形一覧 ver 1.1 aggregated by written form across lemma rows, excluding 補助記号 / 空白 / 記号 POS and empty forms (105,020,008 tokens against the lemma list's 104,612,418). A written-form grain, not an inflected-surface grain.
- **CHJ-SUW** 8 eras 奈良 → 昭和 (q40 cache lists, `primary` panel, lemma grain).

## Estimator (pinned)

- **Fitted quantity:** α_cd, the exponent of the discrete power law P(k) ∝ k^−α_cd for type counts k ≥ xmin, by exact discrete maximum likelihood: maximise −α Σ ln k − n ln ζ(α, xmin) over α ∈ (1.01, 6) (Hurwitz zeta, `scipy.special.zeta`). Reported alongside: **α_rank = 1/(α_cd − 1)** (derived), n_tail (types ≥ xmin) and the tail's share of tokens.
- **Matched size:** each leg is resampled to N* tokens by a multinomial draw from its own empirical distribution, B = 1,000 times (`numpy.random.default_rng(20261031)`, legs processed in registry order). A cell's value is the **median** over replicates; its band is the 2.5–97.5 percentile.
- **Sizes and cutoffs, at a fixed relative cutoff xmin/N* = 5×10⁻⁵:**
  - **Primary: N* = 200,000, xmin = 10.** Every leg except CHJ 奈良 (125,907 tokens).
  - Secondary: N* = 100,000, xmin = 5 (every leg); N* = 1,000,000, xmin = 50 (legs ≥ 1,000,000 tokens).
- **Estimator conventions** (for the estimator factor only, on the reference leg at N* = 200,000): MLE at xmin 5, 10, 30 and 100; OLS slope of log frequency on log rank over ranks 10–1,000.
- **As-published table (no bands):** each leg at its full size with the archive's convention, `powerlaw` 2.0.0 `Fit(discrete=True, estimate_discrete=True)`. Report auto-xmin α_rank with xmin, and the fixed-xmin grid 300 / 500 / 1,000 / 1,500 (cells with n_tail < 50 left blank).

## Regression gates (Type-A; run first)

Full-size `powerlaw` fits must reproduce, to 4 decimals of α_rank and exact xmin:

- q02 `config_fits.csv` (5 surface configs);
- q34 `config_fits_lemma.csv` (8 lemma configs);
- q07 `register_fits.csv` (ALL + 13 registers);
- q16 `luw_fits.csv` (ALL + 13);
- q03 `strata_fits.csv` (the five strata, where token counts match);
- q23 `four_legs_fullN.csv` Bible row (auto and grid);
- q43 `bccwj_xmin_matched.csv` SUW and LUW grids;
- q40 `panel_fits.csv` SUW primary rows (implied rank slope and xmin).

A failed gate is reported with both values. That leg's as-published cells are withheld, and its matched-size cells still run (they do not depend on the gate). If more than three legs fail, stop and report before running anything else.

Separately, the exact MLE is checked against `powerlaw`'s fixed-xmin estimate on every leg at full size and xmin 1,000; differences are reported, not gated.

## Factors and the spread statistic (primary convention)

| Factor | Levels |
|---|---|
| analyzer, surface | AOZ-surf × 5 |
| analyzer, lemma | AOZ-lem × 8 |
| grain | AOZ-surf-mecab-unidic vs AOZ-lem-mecab-unidic (primary); BIB surf vs lem and BCW writtenform vs SUW-ALL reported |
| corpus | AOZ-lem-mecab-unidic, BCW-SUW-ALL, BIB-lem |
| register | BCW-SUW × 13 |
| stratum | BCW-SUW × 5 strata |
| unit | BCW-SUW-ALL vs BCW-LUW-ALL (per-register SUW−LUW differences reported) |
| era | CHJ × 7 (奈良 excluded at N*; descriptive only) |
| estimator | the five conventions above on BCW-SUW-ALL |
| size | BCW-SUW-ALL at N* = 100k / 200k / 1M (fixed relative cutoff) |

- **Spread S_F** = max − min of α_rank over the factor's levels, computed within each replicate b (replicates are independent across legs), giving a distribution of S_F. Reported: the median and 95% band, plus the SD across levels.
- **Noise floor for K levels:** the range of K independent replicates of the reference leg BCW-SUW-ALL, from the same replicate stream. A factor *exceeds noise* if S_F,b > noise_K,b in at least 97.5% of replicates.
- **Reference leg:** BCW-SUW-ALL.

## Predictions (from the dossier; tested on per-replicate differences of spreads)

| | Prediction | Source |
|---|---|---|
| P1 | S(analyzer, surface) < S(register) | §5.3 |
| P2 | S(register) < S(stratum) | §5.3 |
| P3 | S(analyzer, lemma) > S(analyzer, surface) | q34 |
| P4 | S(unit) > S(register) | §5.3, q43 |
| P5 | S(corpus) < S(register) | q23 |
| P6 | S(estimator) > S(register) | q23 |
| P7 | lemma α_rank > surface/written-form α_rank in each of AOZ, BIB and BCW | q23 / q34 (Aozora 1.0485 > 1.0162) |

**Verdict per prediction:** *supported* if the 95% percentile interval of the per-replicate difference excludes 0 in the predicted direction; *contradicted* if it excludes 0 in the other direction; otherwise *unresolved*. P7 gives one verdict per corpus. The seven are reported unadjusted; as a multiplicity check, the report also states whether each verdict survives a 99.3% interval (Bonferroni over seven).

## Known limits, stated in advance

- Multinomial resampling treats tokens as independent. Real text is bursty, so the bands are lower bounds on uncertainty (q10's block bootstrap is the comparison).
- Corpora differ in token filters and dictionaries: Aozora keeps kana/kanji/alphanumeric tokens; the Bible drops symbol POS; BCCWJ uses NINJAL's own list. Corpus and grain contrasts carry these differences.
- The range statistic grows with the number of levels. The noise floor is computed per K for that reason, and the SD is reported beside the range.
- Heaps β, character units and the period-dictionary diachronic legs are outside stage 1.
