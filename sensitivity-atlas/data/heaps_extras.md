# Stage 2 post-hoc descriptives

| stream | L < 1 | L ≥ 1 (windows) | β_text > full list | locality (mean) | order (mean) | net (mean) | r(order, −log₁₀ L) |
|---|---:|---:|---:|---:|---:|---:|---:|
| AOZ-surf-mecab-unidic | 97.8% | 11 | 25.6% | -0.1165 | +0.0807 | -0.0358 | 0.925 |
| AOZ-lem-mecab-unidic | 95.0% | 25 | 27.0% | -0.1173 | +0.0767 | -0.0406 | 0.932 |
| BIB-surf | 95.2% | 24 | 73.6% | -0.0446 | +0.0953 | +0.0507 | 0.963 |
| BIB-lem | 96.6% | 17 | 71.4% | -0.0475 | +0.1044 | +0.0569 | 0.965 |

Q4 robustness: Spearman ρ(list β, α_rank) over 45 lists (Aozora thinned to the MeCab+UniDic surface and lemma lists) = -0.7038.

Unit, LUW − SUW over the 14 BCCWJ lists (ALL + 13 registers): α (stage 1) median -0.0079, positive in 6; β (stage 2) from +0.0773 to +0.2715, positive in 14.
