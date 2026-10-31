# Build report

## Input hashes

- `BCCWJ_WritingFormTable.xlsx` `2c069605b7a64a2d6b09e3086b14ed92fb4a497b1f279bc9bb9478b773f940af`
- `BCCWJ_WritingFormTable_byRegister_Pt1.xlsx` `3463ef0522125d32ed36d928aada7545c043958672598802aebec5ce604e5c51`
- `BCCWJ_WritingFormTable_byRegister_Pt2.xlsx` `2b00038254b25362d205e87876bb30b1445b14f89bed70c5660f64279dea1804`
- `kanjidic2.xml.gz` `9786f2c2c7df568da5042e4c9bbb3f23667895ddb5c64d82d585d8a1616b85b0`
- `kanjivg-20260714.xml.gz` `d4a8e5e11533fe6a5d81a158b2c153964322641d2982c90a919461f3c06dca6b`
- `msgothic.ttc` `4bde3e6392b96910fb59094c6c1a4dbfae18fee78d0bf13dc30616837c4f95db`

## Stroke sources

- KANJIDIC2: 13,108 characters; KanjiVG r20260714: 6,703 characters.
- Kanji in both: 6,417; identical stroke count for 6,309 (98.32%); KanjiVG − KANJIDIC differences: {-3: 1, -2: 4, -1: 35, 1: 64, 2: 3, 4: 1}.

## Pooled table

- Rows: 190,369; distinct (語彙素ID, 読み, 品詞) keys: 190,369; duplicated keys: 0.
- Rows whose parsed form count ≠ 書字形数: 0; whose parsed frequency sum ≠ 合計頻度: 0.
- `BCCWJ_WritingFormTable_byRegister_Pt1.xlsx`: 371,883 rows.
- `BCCWJ_WritingFormTable_byRegister_Pt2.xlsx`: 343,479 rows.

## Register tables

- (key, register) pairs in both parts: 0; row keys in both parts: 0.
- Registers: [('LB', 131289), ('OB', 56492), ('OC', 63374), ('OL', 5165), ('OM', 28305), ('OP', 37893), ('OT', 27976), ('OV', 19039), ('OW', 28099), ('OY', 82063), ('PB', 127635), ('PM', 65985), ('PN', 42047)].
- Register row keys absent from pooled: 0; keys whose register totals ≠ pooled 合計頻度: 0 (of 190,369); pooled keys absent from register tables: 0.
- Register rows whose parsed form count ≠ 書字形数: 0.

## Characters

- Distinct non-space characters in writing forms: 6,777.
- With no stroke count in either source (cost 1 stroke in C2): 352.
- With no MS Gothic glyph (C4 undefined; any form containing one is NaN in C4): 1.

## Writing forms

- Form records by script: {'kanji': 127582, 'katakana': 72569, 'mixed': 49834, 'hiragana': 27197, 'other': 10105, 'empty': 11}.
