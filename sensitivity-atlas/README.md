# Which analysis choices move a Japanese Zipf exponent? A sensitivity atlas, stage 1

Measured 2026-10-31. Published 2026-10-31.

Designed, measured and written up by Claude Opus 5.5 (2026-10-31), then reviewed
by a separate Claude instance that had not written the code.

A Zipf exponent for Japanese depends on many analysis choices: the
morphological analyzer and dictionary, whether words are counted as surface
forms or lemmas, short or long word units, the corpus, its register, the lexical
stratum (語種), the historical period, the fitting method and the corpus size.
The survey this repository belongs to measured these one at a time, each with
its own fitting choices. Its summary ranked them: analyzer < register < stratum
< unit. This set re-fits 57 word-frequency lists with **one** method at **one**
token count, to see which ranking survives. It records seven findings, A1–A7.

- [Data and method](#data-and-method)
- [A1. Register and lexical stratum move the exponent most](#a1)
- [A2. Corpus comes next; the Bible is steeper than prose](#a2)
- [A3. Analyzer and lemma-vs-surface choices matter little](#a3)
- [A4. Short vs long word units: no fixed effect overall, a large one inside registers](#a4)
- [A5. The fitting cutoff matters moderately at a fixed size, and a lot under automatic selection](#a5)
- [A6. Across the CHJ eras the head flattens from Heian to Taishō](#a6)
- [A7. Two settings of the same fitting library disagree by up to 0.27](#a7)
- [Predictions](#predictions)
- [Deviations from the pre-registration](#deviations-from-the-pre-registration)
- [Data files](#data-files)

## Data and method

**The 57 lists** (`data/legs.csv` gives the size of each):

| Group | Lists | Source |
|---|---|---|
| Aozora Bunko, surface forms | 5 analyzer setups: MeCab with UniDic or IPAdic, Sudachi modes A/B/C | ~38 million tokens of cleaned text, tokenised earlier in the survey |
| Aozora Bunko, lemmas | 8 setups: the five above, plus Sudachi's normalised form in modes A/B/C | same text |
| Bible (Japanese), surface and lemma | 2 | Christodoulopoulos & Steedman (2015) parallel Bible corpus, 978,070 tokens, MeCab + UniDic |
| BCCWJ, short units | whole corpus + 13 registers | NINJAL's published frequency list (Maekawa et al., 2014), lemma grain |
| BCCWJ, long units | whole corpus + 13 registers | NINJAL's long-unit list, lemma grain |
| BCCWJ, lexical strata | 和語, 漢語, 外来語, 混種語, 固有名詞 | cut from the short-unit list by its 語種 column |
| BCCWJ, written forms | 1 | NINJAL's writing-form table (書字形一覧), summed over words by written form; symbols excluded |
| Corpus of Historical Japanese (CHJ) | 8 eras, 奈良 to 昭和 | NINJAL's integrated frequency table, short units, lemma grain |

Every input file is identified by its SHA-256 in `data/input_hashes_public.csv`.
Most inputs are frequency lists built earlier in the survey; their names
follow that survey's own folders.

**Method.** Each list's word counts are treated as a sample from a discrete
power law, P(k) ∝ k^−α for counts k at or above a cutoff, xmin.

- **Fit.** α is fitted by exact maximum likelihood and converted to the more
  familiar rank exponent, α_rank = 1/(α − 1) (Clauset et al., 2009; Corral et
  al., 2020). α_rank ≈ 1 is classic Zipf.
- **Common size.** Lists differ in size by three orders of magnitude, and
  exponents depend on size. So each list is resampled to **200,000 tokens** by
  drawing tokens at random from its own word distribution, 1,000 times, and
  fitted each time at **xmin = 10**.
- **Reported values.** Each value below is the median of those 1,000 fits. Each
  interval is the middle 95% of them.
- **What this fits.** At this cutoff a list's top 1,100–3,600 or so word types
  are fitted, covering 68–97% of its tokens.
- **Other sizes.** The same is done at 100,000 tokens (xmin 5; all 57 lists)
  and 1,000,000 tokens (xmin 50; the 45 lists that large), keeping the cutoff
  at the same share of the token count. The main size covers 56 lists, all but
  CHJ 奈良.
- **One factor at a time.** A factor's effect is the **range** of α_rank across
  its levels, such as the 13 registers, computed within each of the 1,000
  resamples.
- **Noise floor.** It is compared with the range you would see from sampling
  noise alone: the range of the same number of independent resamples of the
  BCCWJ whole-corpus list.

**Pre-registration.** The method, the factors and seven predictions were fixed
before any of these fits ran: `data/PREREGISTRATION-STAGE1.md`, SHA-256
`32dcedefd7843c7d2ef6bae16bd61ed879ace62e3c3370197644f777f3cd70e8`. It uses the
project's internal labels: "q07", "q23" and so on are earlier measurements in
this survey, "§5.3" is the survey chapter that ranks the factors, and `src/`
names the project's unpublished scripts.

**Checks against earlier numbers.** Before anything else, every list was also
fitted at full size with the survey's earlier method (the `powerlaw` package,
Alstott et al., 2014), and the result compared with 126 archived values
(`data/gates.csv`). See [A7](#a7) for what that turned up.

<a id="a1"></a>

## A1. Register and lexical stratum move the exponent most

| Factor | Levels | Range of α_rank | 95% interval | Noise floor |
|---|---:|---:|---|---:|
| lexical stratum (語種) | 5 | 0.576 | 0.549–0.603 | 0.018 |
| register | 13 | 0.555 | 0.528–0.582 | 0.027 |

- **Register.** Laws are the steepest (1.482); white papers (1.273), Diet
  records (1.205) and public-relations papers (1.184) follow. Magazines are the
  flattest (0.927). The earlier register spread of 0.87–1.12 was measured far
  out in each list's tail; in the head the registers differ much more.
- **Stratum.** 固有名詞 0.842, 漢語 1.116, 外来語 1.234, 和語 1.275, 混種語 1.418.
  Fitted far out in the tail with an automatically chosen cutoff, 外来語 (0.568)
  and 漢語 (0.680) both sat well below 和語 (1.248). The ordering of strata
  therefore depends on the part of the distribution fitted.
- The two factors cannot be told apart from each other (see P2 below).

<a id="a2"></a>

## A2. Corpus comes next; the Bible is steeper than prose

At lemma grain, BCCWJ gives 0.983, Aozora 1.025 and the Bible 1.225: a range of
0.242 (0.216–0.266). At surface grain, Aozora is 0.992 and the Bible 1.160.

An earlier measurement in this survey compared the lists at a common
*absolute* cutoff on their own sizes, 1 million and 38 million tokens. That
picks different regions of the two distributions, and it put the Bible's higher
value down to the cutoff. At a matched size and a matched relative cutoff, the
Bible's head stays steeper.

<a id="a3"></a>

## A3. Analyzer and lemma-vs-surface choices matter little

| Factor | Levels | Range | 95% interval | Noise floor | Beats noise in |
|---|---:|---:|---|---:|---:|
| analyzer, lemma grain | 8 | 0.054 | 0.036–0.075 | 0.023 | 99.7% |
| surface vs lemma (Aozora, MeCab + UniDic) | 2 | 0.033 | 0.011–0.056 | 0.008 | 95.7% |
| analyzer, surface grain | 5 | 0.024 | 0.008–0.042 | 0.018 | 69.2% |

"Beats noise in" is the share of resamples in which the factor's range exceeded
the noise-only range. Only the lemma-grain analyzer range clears the
pre-registered bar of 97.5%.

Lemmas give a steeper exponent than surface forms:

- Aozora: +0.033 (+0.011 to +0.056).
- Bible: +0.066 (+0.036 to +0.092).
- BCCWJ, lemma against written form: no clear difference, +0.010 (−0.012 to
  +0.031).

<a id="a4"></a>

## A4. Short vs long word units: no fixed effect overall, a large one inside registers

Earlier measurements reported BCCWJ's whole-corpus α_rank rising from 0.984 for
short units (SUW) to 1.300–1.309 for long units (LUW). Those values came from
automatically chosen cutoffs: 4,979 for SUW against 5–6 for LUW, which are very
different parts of the two distributions. Here, long minus short for the whole
corpus is:

| Size (xmin) | LUW − SUW | 95% interval |
|---|---:|---|
| 100,000 (5) | −0.002 | −0.029 to +0.024 |
| 200,000 (10) | +0.011 | −0.015 to +0.035 |
| 1,000,000 (50) | +0.021 | +0.003 to +0.039 |
| full size, xmin 300 | −0.160 | point values 1.190 vs 1.350 |
| full size, xmin 1,500 | −0.099 | point values 1.073 vs 1.171 |

So the unit difference has no fixed size: its size, and even its sign, depend
on the region fitted. Inside registers it is large at the main size. Long units
are flatter in:

- laws, −0.337 (−0.367 to −0.304);
- white papers, −0.224;
- public-relations papers, −0.157;
- newspapers, −0.115;
- textbooks, −0.072;
- Diet records, −0.052.

They are slightly steeper in library books (+0.040), Q&A (+0.041) and
best-sellers (+0.031). One untested explanation: long units merge the compounds
that dominate formal registers.

<a id="a5"></a>

## A5. The fitting cutoff matters moderately at a fixed size, and a lot under automatic selection

On the BCCWJ whole-corpus list at 200,000 tokens, four cutoffs (xmin 5, 10, 30,
100) and a least-squares slope over ranks 10–1,000 span 0.165 (0.128–0.204).
That is less than register.

Size alone, at a matched relative cutoff (100,000 / 200,000 / 1,000,000 tokens),
spans only 0.015 (0.003–0.033).

At full size the picture changes. The automatic cutoff and a fixed one fall in
very different places, and the gap is large:

| List | Automatic cutoff (xmin) | α_rank, automatic | α_rank, xmin 300 |
|---|---:|---:|---:|
| BCCWJ, short units | 4,979 | 0.984 | 1.350 |
| 漢語 | 11,508 | 0.680 | 1.406 |

The second gap, 0.73, is larger than any factor in this set. Comparisons
between exponents from automatically chosen cutoffs mix the factor of interest
with the region fitted.

<a id="a6"></a>

## A6. Across the CHJ eras the head flattens from Heian to Taishō

CHJ short units at 200,000 tokens:

| Era | α_rank |
|---|---:|
| 平安 | 1.300 |
| 鎌倉 | 1.217 |
| 室町 | 1.160 |
| 江戸 | 1.071 |
| 明治 | 1.003 |
| 大正 | 0.999 |
| 昭和 | 1.089 |

The range is 0.304 (0.280–0.330). The fall from 平安 to 大正 does not continue:
昭和 rises again, though its panel is small (407,984 tokens), and
奈良 (125,907 tokens) appears only at 100,000 tokens. The pre-registration
treats era as descriptive: the panels differ in genre as well as period.

<a id="a7"></a>

## A7. Two settings of the same fitting library disagree by up to 0.27

The full-size check reproduced 107 of 126 archived values. The 19 misses fall
on 12 lists: 11 BCCWJ long-unit lists and the BCCWJ verse register. The data
are not the cause; the cause is one setting.

- Some earlier measurements called the `powerlaw` package with its default.
  Others, and this set's pre-registered check, passed
  `estimate_discrete=True`.
- In version 2.0.0, `True` uses a closed-form approximation whenever it gives an
  exponent between 1.5 and 3, as it does for every list here. The default uses
  that approximation only when xmin ≥ 10, and below 10 fits the exact likelihood
  numerically.
- Below xmin 10 the two settings therefore give different exponents, and the
  automatic cutoff search can stop at different cutoffs.
- Re-run with the default, all 24 checks on the 12 lists reproduce
  (`data/gate_variant.csv`). Every archived value is reproduced under the
  setting its source used.

Across the 57 lists, 9 differ by 0.01 or more between the two settings
(`data/variant_gap.csv`):

| List | `estimate_discrete=True`: xmin, α_rank | Default: xmin, α_rank | Gap |
|---|---|---|---:|
| BCCWJ verse (OV), short units | 2, 1.262 | 20, 0.991 | −0.270 |
| BCCWJ library books, long units | 3, 1.337 | 71, 1.161 | −0.176 |
| BCCWJ textbooks, long units | 23, 1.000 | 1, 1.149 | +0.149 |
| BCCWJ Diet records, long units | 3, 1.323 | 12, 1.255 | −0.068 |

An exponent reported with an automatically chosen cutoff should therefore name
the package setting as well. The fits in A1–A6 use neither setting: they use
their own exact maximum likelihood. At xmin 1,000 that estimate agrees with the
package to within 3.2×10⁻⁷ in α on all 52 lists with at least 50 types there.

<a id="predictions"></a>

## Predictions

Each prediction compares the ranges of two factors within each resample. The
middle 95% of the difference decides the verdict; a 99.3% interval checks
whether it survives correction for seven predictions.

| | Prediction | Difference | 95% interval | Verdict | At 99.3% |
|---|---|---:|---|---|---|
| P1 | analyzer (surface) < register | −0.530 | −0.562 to −0.499 | supported | supported |
| P2 | register < stratum | −0.020 | −0.057 to +0.017 | unresolved | unresolved |
| P3 | analyzer (lemma) > analyzer (surface) | +0.030 | +0.005 to +0.055 | supported | unresolved |
| P4 | unit > register | −0.542 | −0.574 to −0.509 | contradicted | contradicted |
| P5 | corpus < register | −0.314 | −0.348 to −0.276 | supported | supported |
| P6 | fitting cutoff/method > register | −0.391 | −0.435 to −0.341 | contradicted | contradicted |
| P7 | lemma > surface: Aozora / Bible / BCCWJ | +0.033 / +0.066 / +0.010 | see A3 | supported / supported / unresolved | supported / supported / unresolved |

- P3's lower bound is close to the bias from comparing a range over 8 levels
  with one over 5, so it is weak support.
- P6 holds for the cutoff factor as defined here (A5): xmin 5–100 and one
  least-squares slope, on one list at a fixed size. It is not a ranking of
  fitting choices in general.

## Deviations from the pre-registration

1. **The check stop rule fired.** The plan said to stop if more than three
   lists failed the full-size check; 12 did. Work stopped. The cause was traced
   to the package setting (A7), and the repository owner chose to continue. The
   default-setting fits (`data/as_published_default.csv`) and the gap table
   were added. The full-size values of the 12 lists are kept rather than
   withheld, beside their default-setting values.
2. **There are 57 lists, not 58.** The plan miscounted; the list registry it
   fixes is unchanged.
3. **Script fixes.** The check script crashed on a summary row after all fits
   were saved. The checks were re-run from the saved fits; no fit changed.
4. **Two small additions.** The spread of level values is reported as the SD of
   level medians. A tail of fewer than 10 types would have returned no estimate;
   it never happened.
5. **Added after review:** the unit-by-size table (`data/unit_by_size.csv`, the
   last section of `data/results.md`).

**Limits:**

- Resampling treats tokens as independent. Real text is bursty, so the
  intervals are lower bounds.
- The corpora differ in token filters and dictionary editions, and corpus
  contrasts carry those differences.
- The written-form list is NINJAL's 書字形 summed across words, not inflected
  surface forms.
- A range grows with the number of levels. That is why the noise floor is
  computed for each count.
- Heaps' law, character units and the period-dictionary historical lists are
  left for later stages.

## Data files

The files are byte-identical copies of the tables the figures above were read
from.

| File | Contents | Used in |
|---|---|---|
| `data/PREREGISTRATION-STAGE1.md` | the frozen plan | method |
| `data/legs.csv` | the 57 lists: corpus, grain, unit, tokens, types | method |
| `data/input_hashes_public.csv` | SHA-256 of every input file | method |
| `data/matched_table.csv` | every list at 100k, 200k and 1M tokens: α_rank median and interval, tail size and token share | A1–A6 |
| `data/factor_spreads.csv` | range per factor, interval, SD of level medians, noise floor, share beating noise | A1–A5 |
| `data/predictions.csv` | P1–P7 | Predictions |
| `data/unit_by_register.csv`, `data/unit_by_size.csv` | long minus short units by register and by size | A4 |
| `data/as_published.csv` | full-size fits with `estimate_discrete=True`: automatic cutoff and the 300–1,500 grid; exact-vs-package check at xmin 1,000 | A4, A5, A7 |
| `data/as_published_default.csv`, `data/variant_gap.csv` | the default-setting fits and the gap between settings | A7 |
| `data/gates.csv`, `data/gate_variant.csv` | the 126 checks against archived values, and the 24 re-checks | A7 |
| `data/results.md` | all matched-size tables in one file | A1–A6 |
| `data/replicates.npz` | every one of the resampled fits (NumPy archive, keys `list|size|quantity`) | A1–A6, Predictions |

## References

- Alstott, J., Bullmore, E., & Plenz, D. (2014). powerlaw: A Python package for
  analysis of heavy-tailed distributions. *PLoS ONE*, 9(1), e85777.
  https://doi.org/10.1371/journal.pone.0085777
- Christodoulopoulos, C., & Steedman, M. (2015). A massively parallel corpus:
  the Bible in 100 languages. *Language Resources and Evaluation*, 49(2),
  375–395. https://doi.org/10.1007/s10579-014-9287-y
- Clauset, A., Shalizi, C. R., & Newman, M. E. J. (2009). Power-law
  distributions in empirical data. *SIAM Review*, 51(4), 661–703.
  https://doi.org/10.1137/070710111
- Corral, Á., Serra, I., & Ferrer-i-Cancho, R. (2020). Distinct flavors of
  Zipf's law and its maximum likelihood fitting: Rank-size and size-distribution
  representations. *Physical Review E*, 102(5), 052113.
  https://doi.org/10.1103/PhysRevE.102.052113
- Maekawa, K., Yamazaki, M., Ogiso, T., Maruyama, T., Ogura, H., Kashino, W.,
  Koiso, H., Yamaguchi, M., Tanaka, M., & Den, Y. (2014). Balanced corpus of
  contemporary written Japanese. *Language Resources and Evaluation*, 48(2),
  345–371. https://doi.org/10.1007/s10579-013-9261-0

### Data and software

- Aozora Bunko (青空文庫), https://www.aozora.gr.jp/
- BCCWJ short- and long-unit frequency lists and writing-form table, National
  Institute for Japanese Language and Linguistics.
  https://clrd.ninjal.ac.jp/bccwj/freq-list.html
- 日本語歴史コーパス (CHJ) 統合語彙表 v2024.03, National Institute for Japanese
  Language and Linguistics, https://doi.org/10.15084/0002000263 (CC BY-NC-SA
  4.0). Only statistics derived from it are published here.
- MeCab via fugashi 1.5.2 with unidic-lite 1.0.8 (UniDic 2.1.2) and IPAdic;
  SudachiPy with SudachiDict core; powerlaw 2.0.0; NumPy; SciPy.
