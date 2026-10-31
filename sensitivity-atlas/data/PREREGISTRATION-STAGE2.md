# Pre-registration — atlas stage 2: Heaps β on the same lists, and word order

Frozen 2026-10-31, before any Heaps quantity of this project was computed. The freeze is this file's SHA-256, stamped in the project log and asserted by `src/heaps.py`. Departures go in the stage 2 report's Deviations section; this file is never edited.

**Known before freezing:**

- all stage 1 results (α_rank per leg, `REPORT-STAGE1.md`);
- the dossier's earlier β values: q02 running-text β 0.580–0.606 across analyzers; q07 and q16 list-expectation β; q28's finding that running-text β exceeds the list expectation in every stratum;
- Chacoma & Zanette (2020), on the lag of new words in original order;
- the Aozora token stream's metadata, which matches the stage 1 Aozora lists on tokens and types.

No Heaps curve had been computed in this project.

## Question

Stage 1 ranked the analysis choices that move Zipf's α under one convention. **Stage 2 asks the same of Heaps' β**, the exponent of vocabulary growth V(n) ∝ n^β, and adds what frequency lists cannot show: **how much the real order of words changes vocabulary growth.**

## Two definitions of β, kept apart

1. **List β (all legs).** The vocabulary growth expected if the tokens were in random order.
   - Each leg is resampled to N* = 200,000 tokens, B = 1,000 times, exactly as in stage 1 (multinomial draw from the leg's own distribution; `numpy.random.default_rng(20261101)`; legs in registry order). CHJ 奈良 (125,907 tokens) is excluded at this size, as in stage 1.
   - For each replicate, E[V(n)] is the hypergeometric rarefaction expectation: E[V(n)] = Σᵢ [1 − C(N* − kᵢ, n) / C(N*, n)] over its types' counts kᵢ.
   - **Primary β** = the OLS slope of log₁₀ E[V(n)] on log₁₀ n over 11 log-spaced points from n = 10⁴ to 10⁵.
   - A cell's value is the median over replicates; its band is the 2.5–97.5 percentile.
   - Also reported, without bands: β over n = 10³ to 10⁴ from the same replicates; and the deterministic full-list expectation E[V(n)] = Σᵢ [1 − (1 − pᵢ)ⁿ] at the primary window (all legs) and at 10⁵–10⁶ (legs ≥ 1,000,000 tokens).
2. **Running-text β (Aozora and Bible only).**
   - Aozora: the q28 token stream (MeCab + UniDic-lite, 38,618,336 tokens; surface and lemma columns).
   - Bible: re-tokenized from q09's `Japanese.xml` with q23's method, in verse order (978,070 tokens; surface and lemma).
   - Gate (Type-A): each stream's aggregated counts must equal the matching stage 1 list exactly (AOZ-surf / AOZ-lem mecab-unidic; BIB-surf / BIB-lem). On failure, stop and report.
   - M = 500 windows of 100,000 consecutive tokens are drawn per stream, starts uniform on [0, N − 100,000] (`default_rng(20261102)`).
   - In each window, V_text(n) is the number of types in the window's first n tokens, on the same 11-point grid; V_shuf(n) is the window's own rarefaction expectation.
   - **Measures per window:**
     - β_text, the OLS slope of log V_text on log n over 10⁴–10⁵;
     - the lag ratio L = V_text(10⁴) / V_shuf(10⁴);
     - β_shuf, the slope of the window's own rarefaction (reported only: β_text − β_shuf is forced up by a lag, because both curves end at the same vocabulary at n = 10⁵).
   - Reported: the median and the 2.5–97.5 percentile over windows.

## Factors and spread (list β, primary)

The factors are the same as stage 1 and use the same levels: analyzer (surface, 5), analyzer (lemma, 8), grain (Aozora), corpus (Aozora lemma, BCCWJ, Bible lemma), register (13), stratum (5), unit (SUW vs LUW, ALL), and era (CHJ, 7; descriptive only).

- **Spread S_F** = max − min of β over the levels within each replicate.
- **Noise floor for K levels** = the range of K independent replicates of BCW-SUW-ALL, drawn after all legs from the same stream.
- A factor *exceeds noise* if S_F > noise_K in ≥ 97.5% of replicates.
- Also reported: the SD of level medians, and per-register LUW − SUW.

## Predictions

| | Prediction | Basis | Test |
|---|---|---|---|
| Q1 | S_β(analyzer, surface) < S_β(register) | stage 1 P1; q02's narrow β band | per-replicate difference of ranges |
| Q2 | S_β(register) < S_β(stratum) | stage 1 P2 | as above |
| Q3 | S_β(unit) > S_β(register) | LUW has ~13× the types of SUW (q43) | as above |
| Q4 | Across the 56 legs, Spearman ρ(β list median, stage 1 α_rank median) < 0 | Heaps–Zipf relation, β ≈ 1/α asymptotically | ρ with a 95% bootstrap over legs (B = 2,000, `default_rng(20261103)`) |
| Q5 | L < 1 in each of the four streams: new types appear later in original order than shuffled | Chacoma & Zanette (2020) | 97.5th percentile of L over windows < 1 |
| Q6 | Median β_text > full-list β over 10⁴–10⁵, in each of the four streams | q28's positive "method effect" | 2.5th percentile of β_text over windows > the full-list β |

**Verdicts:**

- Q1–Q3: *supported*, *contradicted* or *unresolved* by the 95% interval of the per-replicate difference, with a 99.3% check, as in stage 1.
- Q4: *supported* if the bootstrap 95% interval of ρ lies below 0.
- Q5 and Q6: one verdict per stream. *Supported* as stated in the table; *contradicted* if the interval lies wholly on the other side (Q5: 2.5th percentile of L > 1; Q6: 97.5th percentile of β_text < the full-list β); otherwise *unresolved*.

## Known limits, stated in advance

- **List β is a random-order quantity by construction.** It cannot show burstiness; that is what the stream measures are for, and they exist only for Aozora and the Bible.
- **β is window-dependent** (Sano et al., 2012). The primary window is 10⁴–10⁵ tokens at N* = 200,000; other windows are reported, not tested.
- **The windows start in the middle of the text.** A 100,000-token Aozora window often spans several works, and a Bible window spans books. Their bands describe heterogeneity across the corpus, not sampling error of one text.
- **Stratum lists** grow over that stratum's tokens only (a sub-vocabulary), not over running text.
