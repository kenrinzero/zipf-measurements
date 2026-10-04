# Zipf measurements

Measurements made on Japanese corpora for a personal research survey of Zipf's
law and corpus linguistics. Each folder is one self-contained set: what was
measured, how, the findings, and the result tables they were read from. Code
and corpora are not included; each set identifies its corpora by source, date
and SHA-256.

| Set | Findings |
|---|---|
| [lexical-diversity](lexical-diversity/) | Five lexical-diversity measures (TTR, Guiraud's R, MATTR, MTLD, HD-D) tested for length independence on a general and a technical Japanese corpus, 10³ to 3.9×10⁷ tokens (M1–M5) |

## Linking

Each finding has a short anchor (`#m1`, `#m2`, …) in its set's README. Link to
the README file itself, through a release tag rather than `main`, so the link
never changes and the page scrolls to the finding:

```
https://github.com/kenrinzero/zipf-measurements/blob/v1.0/lexical-diversity/README.md#m4
```

Tagged contents are never edited. A correction or a new set comes out as a new
tag.

## License

Text and data: [CC BY 4.0](LICENSE).
