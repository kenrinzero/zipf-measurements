# Kanji or kana: is Japanese spelling choice cost-efficient?

Measured 2026-10-31. Published 2026-10-31.

Designed, measured and written up by Claude Opus 5.5 (2026-10-31), then reviewed
by a separate Claude instance that had not written the code.

Many Japanese words can be written in more than one way: 山, やま and ヤマ are
the same word, "mountain". If writing behaves like an efficient code, the forms
people actually use should be cheaper than the forms they could have used. This
is the logic behind Zipf's law of abbreviation and Ferrer-i-Cancho's
optimal-coding account of it (Ferrer-i-Cancho et al., 2013). "Cheaper" needs a
cost. This set fixes four costs in advance, tests them on the writing-form table
of a large Japanese corpus, and records six findings, K1–K6.

- [Data and method](#data-and-method)
- [K1. Chosen forms are shorter than chance, and less visually complex](#k1)
- [K2. Chosen forms are not lower in strokes](#k2)
- [K3. A word's most frequent form is usually its shortest, rarely its simplest to write](#k3)
- [K4. The more frequent the word, the stronger the pull to the shorter form](#k4)
- [K5. Formal and informal registers barely differ](#k5)
- [K6. In this data every cost mostly measures "kanji or not"](#k6)
- [Deviations from the pre-registration](#deviations-from-the-pre-registration)
- [Data files](#data-files)

## Data and method

**Corpus.** The Balanced Corpus of Contemporary Written Japanese (BCCWJ;
Maekawa et al., 2014) covers books, magazines, newspapers, white papers, laws,
blogs, Q&A sites, textbooks, verse and more. Its publisher, NINJAL, also releases
a writing-form table (書字形一覧, version 1.1). For every word it lists each
written form found in the corpus and how often it occurs, both overall and for
each of 13 registers. These counts have been described by part of speech,
register and frequency (Yamazaki, 2023). We found no earlier test of them against
a cost.

- A word is one row of that table, keyed by lemma ID, reading and part of
  speech. Verbs are keyed by inflected form, as the table itself does. There are
  190,369 rows.
- Every row's form counts add up to its stated total, and every row's 13
  register counts add up to its overall total (`data/build_report.md`).

**Which words.** The test needs words that are written both in kanji and in kana.

- *Strict set* (main): words with at least one all-kanji form and one all-kana
  form: 11,915 words.
- *Broad set*: also counts forms mixing kanji and kana, such as 行く: 19,451
  words.

All of a word's attested forms are its options.

**Costs.** A form's cost is the sum over its characters.

| | Cost | Per character | Stands for |
|---|---|---|---|
| C1 | strokes, kana as 1 | kanji: stroke count (KANJIDIC2); anything else: 1 | the cost named in an earlier run of this test for the same survey |
| C2 | all strokes | kanji: KANJIDIC2 strokes; kana and other characters: KanjiVG strokes; 1 for the 352 characters in neither | handwriting effort, with kana charged fairly |
| C3 | characters | 1 | length on the page |
| C4 | visual complexity | perimetric complexity of the MS Gothic glyph, rendered at 128 px (Pelli et al., 2006) | how busy the glyphs look to a reader |

The two stroke sources agree on 98.3% of the 6,417 kanji they share.

**Test.** For each word:

- *observed cost* is the average cost of its forms, weighted by how often each
  form is used;
- *chance cost* is the plain average over its forms. This is what you would
  expect if the usage counts were shuffled at random among the forms.

**R** is total observed cost divided by total chance cost. Words are weighted by
their frequency (main) or counted once each. R below 1 means the forms people use
are cheaper than chance.

- Significance: a permutation test that shuffles usage counts within each word
  2,000 times.
- Uncertainty: 95% intervals from 2,000 bootstrap resamples of words.
- Correction: Holm across the four main results (strict set, frequency weights).
  A cost counts as *efficient* or *inefficient* only if the corrected p is below
  0.05 and the interval excludes 1.

**Pre-registration.** The plan above, including the verdict rule, the frequency
and register tests and their expected directions, was fixed before any result of
this run was computed: `data/PREREGISTRATION.md`, SHA-256
`6851eb8c54e73d0328968556423e88cdfa4431ad979b9320ef217ef6145377ce`. Only the
earlier run's C1 results (below) were known at that point. The plan uses the
project's internal labels: "q37" is the earlier run, "doc 12" is a chapter of the
survey, and `PRIOR-ART.md`, `REPORT.md` and `src/` are project files not
published here.

**Replication.** The earlier run got R = 1.19 / 1.22 (strict, frequency /
equal weights) and 1.01 / 1.16 (broad) for cost C1. This run reproduced all four,
and the two set sizes, before computing anything else.

<a id="k1"></a>

## K1. Chosen forms are shorter than chance, and less visually complex

Strict set, frequency weights:

| Cost | R | 95% interval | p | Holm p | Verdict |
|---|---:|---|---:|---:|---|
| C1 strokes, kana as 1 | 1.189 | 1.077–1.307 | 0.033 | 0.066 | cost-neutral |
| C2 all strokes | 1.008 | 0.937–1.085 | 0.893 | 0.893 | cost-neutral |
| C3 characters | 0.791 | 0.759–0.828 | 0.0005 | 0.002 | efficient |
| C4 visual complexity | 0.900 | 0.858–0.943 | 0.012 | 0.036 | efficient |

The two efficient costs stay below 1 in every other cell:

- broad set: 0.858 (C3) and 0.912 (C4);
- equal weights: 0.874 and 0.979;
- with rare forms (under 1% of a word's uses) removed: 0.929 (0.869–0.985) and
  0.963 (0.938–0.988).

Removing rare forms shrinks the character effect by about two-thirds. Its sign
holds, but much of its size comes from rare spellings.

<a id="k2"></a>

## K2. Chosen forms are not lower in strokes

Neither stroke cost passes on the main cells (table above). With each word
counted once, both sit above chance: 1.223 for C1 and 1.106 for C2, each with
p ≤ 0.0005. So the typical word is written in a form with *more* strokes than a
random choice among its forms would give. Charging kana their real strokes (C2)
brings the main result down to about chance (1.008), but not below it, and the
equal-weight result stays above 1.

<a id="k3"></a>

## K3. A word's most frequent form is usually its shortest, rarely its simplest to write

Strict set, each word counted once:

| Cost | Most frequent form is a cheapest form | By chance |
|---|---:|---:|
| C1 strokes, kana as 1 | 23.0% | 43.7% |
| C2 all strokes | 22.8% | 37.7% |
| C3 characters | 72.3% | 49.9% |
| C4 visual complexity | 38.8% | 34.8% |

<a id="k4"></a>

## K4. The more frequent the word, the stronger the pull to the shorter form

For each word with at least 100 uses, an efficiency index e = (observed − chance)
÷ (dearest − cheapest form cost) is negative when the word's forms in use are
cheaper than chance and positive when they are dearer; it lies between −1 and +1.
Its Spearman correlation with log frequency, strict set:

| Cost | ρ | 95% interval |
|---|---:|---|
| C1 | +0.220 | +0.195 to +0.243 |
| C2 | +0.148 | +0.123 to +0.171 |
| C3 | −0.283 | −0.305 to −0.259 |
| C4 | −0.061 | −0.084 to −0.036 |

- Frequent words lean further toward their shorter form and further away from
  their fewest-strokes form. The character result is a within-word version of
  the law of abbreviation. Visual complexity also meets the plan's test
  (interval below 0), but weakly.
- Across the ten frequency deciles, the mean character index falls from −0.18 in
  the lowest decile to −0.37 in the highest.
- It holds with a threshold of 20 uses (−0.313) and of 1,000 (−0.113), within
  nouns (−0.326), and within native (和語, −0.174) and Sino-Japanese (漢語,
  −0.366) words.
- Verbs and adjectives barely occur in the strict set, because their kanji forms
  carry kana endings. A check added after the pre-registration, on the broad set,
  gives −0.084 (−0.128 to −0.039) for verbs and −0.239 (−0.347 to −0.130) for
  adjectives.

<a id="k5"></a>

## K5. Formal and informal registers barely differ

R was computed per register over the words with at least 10 uses in that
register (632 words for laws up to 8,974 for library books), with each word
weighted by its overall frequency. So registers are compared on partly different
words, and no per-register intervals were computed. With frequency weights the 13
registers span 0.779–0.825 on characters, a narrow band, and 0.974–1.204 on C1,
where verse is the only register below 1. With equal weights laws give the
highest C1 (1.518) and the lowest C3 (0.719).

The pre-registration recorded an expectation, not a test: formal registers use
more kanji, so informal text (Yahoo! Chiebukuro Q&A and Yahoo! Blogs) should sit
lower than formal text (laws, white papers, newspapers) on C1, C2 and C4, and
higher on C3. It also noted that this comparison cannot separate efficiency from
spelling convention. On the 3,490 words with at least 10 uses in both groups, the
difference (informal minus formal) was:

| Cost | Difference | 95% interval |
|---|---:|---|
| C1 | +0.022 | +0.008 to +0.038 |
| C2 | +0.010 | +0.003 to +0.018 |
| C3 | −0.008 | −0.015 to −0.002 |
| C4 | −0.001 | −0.004 to +0.003 |

All the differences are small. On three costs they go the opposite way to the
prediction, and C4 is flat: on shared words, informal text leans very slightly
*more* toward kanji than formal text. One possible reason, untested here, is that
blogs and Q&A posts are typed, and typing a kanji costs no more than typing kana.

<a id="k6"></a>

## K6. In this data every cost mostly measures "kanji or not"

A word's kanji form is shorter than or as long as its kana form in all but 7 of
the 11,915 words (table below), and kanji characters typically carry more
strokes than kana. So a preference for kanji, whatever its cause, would look
efficient on C3 and inefficient on C1. These results are
consistent with an efficient code for a reader-side length cost. They cannot
show that writers minimise length rather than follow a kanji convention.

A check added after the pre-registration looked for a length effect beyond the
kanji preference. For each word it compared the most frequent kanji form with the
most frequent kana form. Kanji's share of the uses was:

| Kana form is longer by | Words | Kanji share, per word | Kanji share, all uses |
|---|---:|---:|---:|
| kana form shorter | 7 | 0.264 | 0.087 |
| 0 characters | 1,053 | 0.601 | 0.149 |
| 1 | 4,595 | 0.712 | 0.638 |
| 2 | 5,034 | 0.730 | 0.843 |
| 3 | 1,074 | 0.715 | 0.931 |
| 4 or more | 152 | 0.631 | 0.862 |

Per word the share is flat once the kana form is longer at all (0.712, 0.730,
0.715), so the per-word view shows no length effect beyond the kanji preference.
The all-uses column rises with the gap, which could be a length effect among
frequent words, as in K4. But its equal-length starting point is dominated by two
particles, which make up 77% of the equal-length uses: の, with a rare kanji form
之 (46%), and は, with a rare form 話 (30%). The check therefore gives no clear
evidence either way.

A second check, also added after the pre-registration, removed particles and
auxiliary verbs. Every main result's sign was unchanged: C1 1.280, C2 1.061,
C3 0.772, C4 0.890.

Further limits:

- BCCWJ is printed and typed text, so its writers never paid a stroke cost.
  Stroke results bear on handwriting only by extension. Typing cost, reading-time
  norms and romaji are not tested; the table has no romaji forms.
- Each word gets one usage profile for the whole corpus (or one per register),
  not one per context.
- C4 rests on one font at one size.

## Deviations from the pre-registration

1. Usage shares are divided by the sum over non-empty forms rather than by the
   row total; this matches the earlier run. Eleven rows have an empty form,
   including 空白 ("blank"), whose only form is a full-width space.
2. Frequency-test groups with fewer than 30 words are not reported, a threshold
   the plan did not state. This left no verb group in the strict set, so the
   plan's robustness check (nouns *and* verbs) could not be run as written. The
   broad-set check in K4 stands in for it.
3. Permutation p is (count + 1) / 2,001, so its floor is about 0.0005.
4. One character has no MS Gothic glyph; one broad-set word is dropped from C4.
5. The script classifier keeps a "latin" class from the earlier run; no form
   falls in it, so it changes nothing.

## Data files

The files are byte-identical copies of the tables the figures above were read
from.

| File | Contents | Used in |
|---|---|---|
| `data/PREREGISTRATION.md` | the frozen plan | method |
| `data/build_report.md` | input hashes, row and register validation, stroke-source agreement | method |
| `data/h1.csv` | R, interval, p, Holm p and verdict for all 16 cost × set × weighting cells | K1, K2 |
| `data/h1b.csv` | most-frequent-form shares against chance | K3 |
| `data/h2.csv`, `data/h2_deciles.csv` | frequency correlations by group and threshold; decile means | K4 |
| `data/h3_registers.csv`, `data/h3_contrast.csv` | R per register; formal against informal | K5 |
| `data/s1.csv` | results with rare forms removed | K1, K2 |
| `data/e1_kanji_share_by_length_gap.csv`, `data/e1_equal_length_top_rows.csv` | the length-gap check and the rows behind its equal-length line | K6 |
| `data/e2_h2_broad_pos.csv` | the verb and adjective check | K4 |
| `data/e3_no_particles.csv` | main results without particles and auxiliaries | K6 |

## References

- Ferrer-i-Cancho, R., Hernández-Fernández, A., Lusseau, D., Agoramoorthy, G.,
  Hsu, M. J., & Semple, S. (2013). Compression as a universal principle of
  animal behavior. *Cognitive Science*, 37(8), 1565–1578.
  https://doi.org/10.1111/cogs.12061
- Maekawa, K., Yamazaki, M., Ogiso, T., Maruyama, T., Ogura, H., Kashino, W.,
  Koiso, H., Yamaguchi, M., Tanaka, M., & Den, Y. (2014). Balanced corpus of
  contemporary written Japanese. *Language Resources and Evaluation*, 48(2),
  345–371. https://doi.org/10.1007/s10579-013-9261-0
- Pelli, D. G., Burns, C. W., Farell, B., & Moore-Page, D. C. (2006). Feature
  detection and letter identification. *Vision Research*, 46(28), 4646–4674.
  https://doi.org/10.1016/j.visres.2006.04.023
- Yamazaki, M. (2023). 『現代日本語書き言葉均衡コーパス』に見る語表記の量的分布
  ―品詞，レジスター，頻度との関係―. In *Proceedings of the 29th Annual Meeting
  of the Association for Natural Language Processing* (pp. 2279–2282).
  https://www.anlp.jp/proceedings/annual_meeting/2023/pdf_dir/P9-11.pdf

### Data and software

- BCCWJ writing-form table (書字形一覧) ver 1.1, National Institute for Japanese
  Language and Linguistics. https://clrd.ninjal.ac.jp/bccwj/freq-list.html
- KANJIDIC2, Electronic Dictionary Research and Development Group (CC BY-SA).
  https://www.edrdg.org/wiki/index.php/KANJIDIC_Project
- KanjiVG, release r20260714, Ulrich Apel (CC BY-SA 3.0).
  http://kanjivg.tagaini.net. Stroke counts from KanjiVG and KANJIDIC2 were
  used to compute the statistics here; neither dataset is redistributed.
