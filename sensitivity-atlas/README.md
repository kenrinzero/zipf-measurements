# Which analysis choices move Japanese Zipf and Heaps exponents? A sensitivity atlas

Measured 2026-10-31. Stage 1 published 2026-10-31 (tag v1.4); stage 2 added
2026-10-31 (tag v1.5).

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
[Stage 2](#stage-2) asks the same of vocabulary growth (Heaps' law), and adds
what frequency lists cannot show: the effect of real word order. It records six
findings, B1–B6.

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
- [Stage 2: vocabulary growth (Heaps' law) and word order](#stage-2)
  - [B1. Register moves vocabulary growth most](#b1)
  - [B2. Long units raise vocabulary growth in every register](#b2)
  - [B3. Vocabulary growth and the Zipf exponent move in opposite directions](#b3)
  - [B4. In real text, new words arrive later than in shuffled text](#b4)
  - [B5. Real text against the shuffled list: no single direction](#b5)
  - [B6. The exponent depends on the stretch of growth measured](#b6)

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
- Heaps' law is covered in [stage 2](#stage-2). Character units and the
  period-dictionary historical lists are left for later stages.

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

<a id="stage-2"></a>

## Stage 2: vocabulary growth (Heaps' law) and word order

Heaps' law describes how fast new words keep appearing as a text gets longer:
the number of distinct words V grows roughly as n^β after n tokens. A higher β
means a richer, less repetitive vocabulary.

**Two kinds of β, kept apart.**

1. **List β (all 57 lists).** The vocabulary growth expected if the tokens came
   in random order.
   - Each list is resampled to 200,000 tokens, 1,000 times, as in stage 1.
   - For each resample, the expected number of distinct words in a random
     subset of n tokens is computed exactly (rarefaction).
   - β is the slope of log V against log n between n = 10,000 and 100,000.
2. **Running-text β (Aozora and the Bible only).** These two exist as running
   text, so real word order can be measured.
   - Five hundred stretches of 100,000 consecutive tokens are drawn from each
     of four streams: Aozora and the Bible, as surface forms and as lemmas.
   - Each stream matches its stage 1 list in tokens, types and the distribution
     of word counts (`data/stream_gates.txt`).

**Pre-registration.** The plan was fixed before any vocabulary-growth quantity
was computed: `data/PREREGISTRATION-STAGE2.md`, SHA-256
`d239ef2830f89d35b42bbc0833400c7e2a3d4a8bc5981912dda30559b8bb1419`.

<a id="b1"></a>

### B1. Register moves vocabulary growth most

List β, range across the factor's levels (`data/heaps_factor_spreads.csv`):

| Factor | Levels | Range | 95% interval |
|---|---:|---:|---|
| register | 13 | 0.312 | 0.308–0.317 |
| lexical stratum (語種) | 5 | 0.195 | 0.191–0.199 |
| era (CHJ; descriptive) | 7 | 0.178 | 0.175–0.182 |
| corpus (lemma) | 3 | 0.163 | 0.159–0.167 |
| unit (short vs long, BCCWJ) | 2 | 0.128 | 0.125–0.130 |
| analyzer, lemma | 8 | 0.050 | 0.047–0.052 |
| surface vs lemma (Aozora) | 2 | 0.043 | 0.040–0.046 |
| analyzer, surface | 5 | 0.030 | 0.027–0.032 |

All of these exceed the resampling noise floor (0.001–0.004). That floor only
measures resampling of one fixed list, though, so almost any two distinct lists
clear it. B5 shows that real text varies far more.

- **Register.** Laws grow their vocabulary slowest (0.357), then white papers
  (0.501). Library and published books grow fastest among registers (0.670,
  0.654). Laws and white papers also had the steepest Zipf heads in stage 1.
- **Lexical stratum.** 固有名詞 0.609, 和語 0.552, 漢語 0.513, 外来語 0.447, 混種語
  0.414. Each is growth within that stratum's own tokens.
- **Era.** 平安 0.482, 鎌倉 0.552, 室町 0.524, 江戸 0.600, 明治 0.661, 大正 0.644,
  昭和 0.544. Growth rises from Heian to Meiji, with a dip in Muromachi; it then eases in
  Taishō and falls in Shōwa, a small panel.
- **Corpus.** BCCWJ 0.653, Aozora 0.655, Bible 0.492.
- **Analyzer.** Surface β runs from 0.680 (MeCab with IPAdic) to 0.710
  (Sudachi C).

<a id="b2"></a>

### B2. Long units raise vocabulary growth in every register

Long minus short units, list β (`data/heaps_unit_by_register.csv`):

- The whole corpus: +0.128 (+0.125 to +0.130).
- Every one of the 13 registers is positive, from +0.077 (verse) to +0.272
  (white papers). Laws are +0.214 and public-relations papers +0.211.

In stage 1 the same comparison for the Zipf exponent had no consistent sign
(median −0.008, positive in 6 of the 14 lists; `data/heaps_extras.md`),
although inside some registers it was large (A4). Long units speed up
vocabulary growth everywhere, but move the Zipf head in no consistent
direction.

<a id="b3"></a>

### B3. Vocabulary growth and the Zipf exponent move in opposite directions

Across the 56 lists fitted at 200,000 tokens, the Spearman correlation between
list β and stage 1's α_rank is −0.616 (95% bootstrap interval −0.799 to −0.365).

Thirteen of those lists are near-duplicate Aozora analyzer setups. Keeping only
the MeCab + UniDic surface and lemma lists (45 lists) gives −0.704. Lists with
steep Zipf heads, such as laws, white papers and the Bible, tend to be the ones
whose vocabulary grows slowly.

<a id="b4"></a>

### B4. In real text, new words arrive later than in shuffled text

For each 100,000-token stretch, the lag L is the number of distinct words in its
first 10,000 tokens, divided by the number expected if that stretch were
shuffled (`data/heaps_streams.csv`, `data/heaps_stream_extras.csv`):

| Stream | Median L | Middle 95% of stretches | Stretches with L < 1 |
|---|---:|---|---:|
| Aozora, surface | 0.842 | 0.625–0.996 | 97.8% |
| Aozora, lemma | 0.847 | 0.636–1.045 | 95.0% |
| Bible, surface | 0.821 | 0.591–1.047 | 95.2% |
| Bible, lemma | 0.801 | 0.564–1.015 | 96.6% |

So a typical stretch of real text has 15–20% fewer distinct words at 10,000
tokens than its own shuffle would: new words come in later than random order
would bring them. This agrees with Chacoma & Zanette (2020).

The pre-registered test was strict: at least 97.5% of stretches had to have L
below 1. Only Aozora surface passes it; in the other three streams, 3.4–5.0% of
stretches sit at or above 1.

<a id="b5"></a>

### B5. Real text against the shuffled list: no single direction

An earlier measurement in this survey found running-text vocabulary growth
above the list's expected growth. Here, over the same n = 10,000–100,000:

| Stream | Running-text β, median | Middle 95% of stretches | Whole-list β | Stretches above the list |
|---|---:|---|---:|---:|
| Aozora, surface | 0.658 | 0.525–0.833 | 0.698 | 25.6% |
| Aozora, lemma | 0.615 | 0.480–0.776 | 0.655 | 27.0% |
| Bible, surface | 0.579 | 0.466–0.735 | 0.537 | 73.6% |
| Bible, lemma | 0.534 | 0.415–0.749 | 0.492 | 71.4% |

The pre-registered test was unresolved in all four streams: β varies too much
from stretch to stretch. A typical Bible stretch grows faster than the list; a
typical Aozora stretch grows slower.

A post-hoc split of the average difference shows two effects pulling against
each other:

| Stream | Locality | Order | Net |
|---|---:|---:|---:|
| Aozora, surface | −0.117 | +0.081 | −0.036 |
| Aozora, lemma | −0.117 | +0.077 | −0.041 |
| Bible, surface | −0.045 | +0.095 | +0.051 |
| Bible, lemma | −0.048 | +0.104 | +0.057 |

- **Locality** is how much flatter a stretch's own shuffled growth is than the
  whole list's. A 100,000-token stretch of Aozora spans only a few works, so it
  draws on a narrower vocabulary than the whole corpus. The Bible is one text,
  so the effect is small.
- **Order** is the extra slope of the real text over its own shuffle. It is the
  lag of B4 in other units: across stretches it correlates with −log₁₀ L at
  0.93–0.97.

The earlier survey measurement used growth over the whole corpus, not
100,000-token stretches. Whether locality explains Aozora's difference at that
scale is not tested here.

<a id="b6"></a>

### B6. The exponent depends on the stretch of growth measured

For every list, β between 1,000 and 10,000 tokens is higher than between 10,000
and 100,000 (`data/heaps_table.csv`). For BCCWJ short units the resampled list
β falls from 0.784 (1,000–10,000) to 0.653 (10,000–100,000). The whole-list β
between 100,000 and 1,000,000 tokens is lower still, 0.519. Sano,
Takayasu & Takayasu (2012) report the same size dependence. Each β in this
stage belongs to its window.

### Stage 2 predictions

| | Prediction | Estimate | 95% interval | Verdict |
|---|---|---:|---|---|
| Q1 | analyzer (surface) range < register range | −0.283 | −0.288 to −0.278 | supported |
| Q2 | register range < stratum range | +0.117 | +0.112 to +0.123 | contradicted |
| Q3 | unit range > register range | −0.185 | −0.190 to −0.180 | contradicted |
| Q4 | ρ(list β, α_rank) < 0 | −0.616 | −0.799 to −0.365 | supported |
| Q5 | lag below 1, each stream | see B4 | | supported for Aozora surface; unresolved for the other three |
| Q6 | running-text β above the whole-list β, each stream | see B5 | | unresolved in all four |

Q1–Q3 keep their verdicts at 99.3% (`data/heaps_predictions.csv`).

**Deviations.** Only post-hoc additions: the locality/order split, the shares
of stretches, the thinned correlation in B3 and the unit comparison in B2 come
from `data/heaps_stream_extras.csv` and `data/heaps_extras.md`. They decide no
verdict.

**Limits:**

- List β assumes random order by construction; burstiness is measured only for
  Aozora and the Bible.
- The stretch bands describe variation across each corpus, not the sampling
  error of one text.
- Stratum β is growth within a sub-vocabulary, not in running text.

### Stage 2 data files

| File | Contents | Used in |
|---|---|---|
| `data/PREREGISTRATION-STAGE2.md` | the frozen plan | stage 2 |
| `data/heaps_table.csv` | every list: list β median and interval, β at 1,000–10,000 tokens, whole-list β at 10⁴–10⁵ and 10⁵–10⁶ | B1, B6 |
| `data/heaps_fulllist.csv` | whole-list β from the full frequency lists | B5, B6 |
| `data/heaps_factor_spreads.csv` | range per factor, interval, noise floor | B1 |
| `data/heaps_unit_by_register.csv` | long minus short units by register | B2 |
| `data/heaps_predictions.csv` | Q1–Q6 | predictions |
| `data/heaps_streams.csv`, `data/heaps_stream_extras.csv` | the running-text stretches: lag, β, shares, locality/order split | B4, B5 |
| `data/heaps_extras.md` | post-hoc checks: thinned correlation, unit comparison with stage 1 | B2, B3 |
| `data/results_stage2.md` | all stage 2 tables in one file | B1–B6 |
| `data/stream_gates.txt` | the four streams against their stage 1 lists | stage 2 |
| `data/heaps_replicates.npz`, `data/stream_windows.npz` | every resample's β, and every stretch's start, β and lag | B1–B5 |

## References

- Alstott, J., Bullmore, E., & Plenz, D. (2014). powerlaw: A Python package for
  analysis of heavy-tailed distributions. *PLoS ONE*, 9(1), e85777.
  https://doi.org/10.1371/journal.pone.0085777
- Chacoma, A., & Zanette, D. H. (2020). Heaps' law and Heaps functions in tagged
  texts: evidences of their linguistic relevance. *Royal Society Open Science*,
  7(3), 200008. https://doi.org/10.1098/rsos.200008
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
- Sano, Y., Takayasu, H., & Takayasu, M. (2012). Zipf's law and Heaps' law can
  predict the size of potential words. *Progress of Theoretical Physics
  Supplement*, 194, 202–209. https://doi.org/10.1143/PTPS.194.202

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
