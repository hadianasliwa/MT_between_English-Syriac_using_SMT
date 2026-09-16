# English–Syriac Parallel Corpus

The first systematically curated English–Syriac parallel corpus for machine translation, covering the complete Bible in the **East Syriac (Madnkhaya) script**.

Syriac (Assyrian Neo-Aramaic) is spoken by an estimated 500,000–1,500,000 people worldwide and is classified by UNESCO as an endangered language. Before this work, no publicly available, systematically aligned English–Syriac dataset existed, and the language was effectively absent from the machine translation literature.

**Both sides of this corpus are public domain**, so the aligned data can be redistributed freely — see [Licence](LICENSE).

---

## Contents

| | |
|---|---|
| **Sentence pairs** | 38,847 verse-aligned English–Syriac pairs |
| **English source** | World English Bible (WEB) — public domain |
| **Syriac source** | Peshitta |
| **Script** | East Syriac / Madnkhaya, Unicode Syriac block (U+0700–U+074F) |
| **Vocalisation** | Unvocalised (consonantal) |
| **Direction** | Aligned; baselines trained English → Syriac |
| **Domain** | Biblical / religious register only |

The **New Testament** (~7,900 verses) was curated from existing digitised parallel sources. The **Old Testament** (~23,000 verses) was built from scratch: PDF text extraction, custom verse-level segmentation anchored on chapter:verse numbering, and a manual alignment review by three bilingual annotators.

---

## Format

CSV, UTF-8, one verse pair per line. English and Syriac are separated by a Comma:

```
english sentence<,>ܣܘܪܝܝܐ
```

For example:

```
The book of the genealogy of Jesus Christ, the son of David, the son of Abraham.,ܟܬܒܐ ܕܝܠܝܕܘܬܗ ܕܝܫܘܥ ܡܫܝܚܐ ܒܪܗ ܕܕܘܝܕ ܒܪܗ ܕܐܒܪܗܡ
```

### Repository layout

```
data/
  raw/          # as extracted, before cleaning
  clean/        # cleaned and normalised parallel files
  splits/       # 95/5, 90/10, 80/20, 70/30 train/test + fixed 1,000-sentence tune set
scripts/
  extract.py           # PDF text extraction
  segment.py           # verse-level segmentation on chapter:verse anchors
  clean.py             # noise and symbol stripping
  devocalise.py        # Syriac diacritic removal by Unicode code point
  verify_encoding.py   # encoding and structure checks (see below)
```

Splits were produced with a fixed shuffle seed of **42**, with a held-out tuning set of **1,000 sentences** fixed across all configurations. Pairs where either side exceeded 100 BPE tokens were removed by `clean-corpus-n.perl`.

---

## Preprocessing

The pipeline applies noise removal, orthographic normalisation, and Byte-Pair Encoding. Both raw and cleaned forms are shipped so you can re-run it with your own choices.

**A note on devocalisation.** Syriac writers routinely omit vowel diacritics, so the same word can appear in several surface forms.



## Baseline results

Phrase-based SMT (Moses, GIZA++, KenLM), word-level BLEU after de-BPE:

| Configuration | Split | Word BLEU |
|---|---|---|
| Baseline (3-gram, dl=6, 10k BPE) | 95/5 | 22.54 |
| Baseline | 90/10 | 21.76 |
| Baseline | 80/20 | 19.85 |
| Baseline | 70/30 | 17.81 |
| **+ OSM + 5-gram LM** | **95/5** | **23.54** |
| + 5-gram LM, dl=12, 20k BPE | 95/5 | 22.63 |

Human evaluation of the best model by 11 native Assyrian speakers: mean adequacy 3.42/5, fluency 3.34/5, with a strong adequacy–fluency correlation (r = 0.865).

Training data volume is the dominant constraint: BLEU falls 4.73 points as the training set shrinks from 35,905 to 26,193 pairs. Adding an Operation Sequence Model gives the single largest gain (+1.00 BLEU), addressing the SVO-to-VS word-order divergence between English and Syriac.

---

## Limitations

The corpus is Biblical text only. It contains no modern vernacular, secular vocabulary, or non-liturgical discourse, and a model trained on it will not generalise to everyday language. BLEU scores here are in-domain and are not comparable across language pairs — only within them. Single-reference evaluation understates real quality for a morphologically rich target language, where a correct translation that differs from the reference in surface form receives no credit.

---

## Citation

```bibtex
@misc{sliwa2026syriac,
  author       = {Sliwa, Hadiana and Hassani, Hossein},
  title        = {Machine Translation between English and Syriac (East Syriac Dialect) using Statistical Machine Learning},
  year         = {2026},
  institution  = {University of Kurdistan Hewl\^er},
}
```

---

## Licence

| Component | Licence |
|---|---|
| English text | Public domain — World English Bible |
| Syriac text | Public domain — Peshitta (ancient text) |
| Alignment, curation, manual review | **CC BY 4.0** |
| Scripts | **MIT** |

The CC BY 4.0 licence covers **our curation, alignment and processing work**, not the scriptural texts, which are public domain in their own right. Full provenance for each source, including edition and download details.

> The processed English files are *derived from* the World English Bible and are not the WEB itself. Per eBible.org's terms, modified text may not be distributed under the "World English Bible" name.

---

## Contact

**Hadiana Sliwa** — hadiana.harun@ukh.edu.krd
**Dr Hossein Hassani** — hosseinh@ukh.edu.krd

Department of Computer Science and Engineering
University of Kurdistan Hewlêr, Kurdistan Region, Iraq
