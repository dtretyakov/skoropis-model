---
authors:
  - name: 'dtretyakov'
id: ''
summary: 'skoropis-12 — Russian chancery cursive (skoropis) of the 17th century, 1620-1720'
license: CC-BY-4.0
software_name: kraken
software_hints:
  - kraken_version>=7.0
  - seg_type=baselines
language:
  - rus
script:
  - Cyrl
model_type:
  - recognition
base_model:
  - '10.5281/zenodo.22912945'
metrics:
  cer: 7.28
keywords:
  - skoropis
  - chancery cursive
  - Muscovite records
  - census books
  - 17th century
  - pre-reform orthography
datasets: []
citation: 'dtretyakov. skoropis-12: a kraken model for 17th-century Russian chancery cursive. 2026. https://doi.org/10.5281/zenodo.22905349'
---

# skoropis-12 — a kraken model for 17th-century Russian chancery cursive

A line recogniser for **приказная скоропись**, the chancery cursive of Muscovite
offices, covering roughly **1620–1720**. No public model reads this hand as of
September 2026: the Slavic models in Transkribus are 16th-century semi-uncial and
11th–16th-century uncial, and the Russian ones begin in the 18th century. This one
fills that gap.

- **Format** — kraken 7, `.mlmodel`, `baselines` segmentation
- **Size** — 15 MB
- **Version** — 5, released September 2026: the weights of version 4 with this card, which describes the data by period; the name stays `skoropis-12` across versions
- **Base model** — version 3 of this record, [10.5281/zenodo.22912945](https://doi.org/10.5281/zenodo.22912945); version 2 was trained from scratch
- **Architecture** — kraken's default recognition VGSL: 4 convolutional blocks, 3 bidirectional LSTM layers, 4.1 M parameters
- **Training** — fine-tuned from version 3, AdamW, cosine schedule, batch 16, lr 1.5e-4, augmentation, the 1760s books repeated six times; epoch 5 chosen on hand-read test lines, not on the internal validation score
- **Codec** — 278 symbols: pre-reform orthography with **ѣ ѳ ѵ ъ**, Cyrillic numerals, titlo abbreviations, superscript letters
- **Licence** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Published** — [10.5281/zenodo.22905349](https://doi.org/10.5281/zenodo.22905349), the concept DOI, which resolves to the newest version

Install it with `kraken get 10.5281/zenodo.22905349`, then run
`kraken -f image -x -i page.jpg out.xml segment -bl ocr -m skoropis-12.mlmodel`.

## Training data

About **7400 annotated pages, 104 000 lines**, from **28 chancery and census books** dated
**1625 to 1764**. They come from the record-keeping of the Siberian Chancery, the Pomestny
Chancery and the revision offices, and cover **the Urals, Western Siberia, the Russian North
and the Vyatka land**: land-tax books, name books, census books and revision tales.

By period, distinct lines:

- **1625–1659** — about 19 900 lines (19 %)
- **1660–1699** — about 22 800 lines (22 %)
- **1700–1720** — about 61 700 lines (59 %)
- **1760s** — about 4 600 lines (4 %), repeated six times in training so that the newest hand is not drowned

The 1760s pages write ages and years in Arabic numerals, and the labels keep them so.
The hand is that of provincial scribes writing for Moscow, which is why a model trained
on it transfers across that whole territory rather than to one office.

The set is not fixed. It grows as more material is aligned, and a later release of this
model may rest on a different mixture within the same period and region.

Editions normalise what the scribe wrote: ages printed as Arabic numbers where the page
spells them out or writes Cyrillic numerals, surnames in capitals. Before training, every
book was checked by eye on several pages and the labels were brought back to what the page
shows; lines whose alignment could not be trusted were discarded rather than kept.

Ground truth was made by aligning page images with transcriptions published by others —
**Yu. V. Konovalov**, the **census1710** project and **Rodnaya Vyatka** — with a smaller
part annotated directly. The model exists because those transcriptions do, and that is a
statement of provenance rather than of courtesy.

## Measured performance

Character accuracy on lines read by hand, from books never shown to the model;
version 3 in brackets, then version 2.

- **1740s** (42 lines, three books) — **56.5 %** (51.3 %, 49.3 %)
- **1718–1720s** (87 lines, four books) — **88.7 %** (88.7 %, 81.9 %)
- **1680s** (101 lines) — **92.7 %** (92.6 %, 92.9 %)
- **1630s**, a hand far from the training set (two pages) — 53.3 % (54.7 %, 51.7 %)
- Internal validation (every 20th page of every book, every 8th of the 1760s books) — 0.9079

The `accuracy` field inside the file is empty: the training history was not preserved
on export, and putting a number there after the fact would pass one measurement off as
another. Every figure above is named together with the period it was measured on.

## Where it fails

**The hand weakens through the middle of the 18th century.** Chancery hands of the 1720s
still read well. The 1740s–60s are only begun: at 56 % of characters the model is good for
finding a formula, not for reading a name, and **soul numbers** in the margins of those books
are not read yet. For 1800–1870 use `Kansallisarkisto/cyrillic-htr-model`; between the 1740s
and about 1800 check a page by eye before trusting any reading.

**Confidence is not correctness.** On an unfamiliar hand a recogniser holds high
confidence under nonsense — measured: on skoropis of 1632 another model returned fluent
Russian lifted from a different kind of document at confidence 0.99. Before trusting a
reading of an unfamiliar case, run the model over a line whose reading is known
independently.

**Characters per line mislead on tabular cases.** A revision list puts given name,
patronymic and age in separate cells, so a flawless reading still comes out near ten
characters a line. There, mean confidence is the measure.

## What is not published here

**The training data.** It is built on archival scans obtained through the remote-access
systems of Russian state archives, whose terms of use forbid reproducing the images.
The weights are not images and carry nothing from a viewer; the pages themselves cannot
be released. Saying so is more honest than leaving the dataset to look merely unfinished.

Machine readings of individual cases produced with this model are open and published in
the [catalogue of censuses](https://dtretyakov.github.io/perepisi-catalog/).

## Citation

> dtretyakov. skoropis-12: a kraken model for 17th-century Russian chancery cursive.
> 2026. https://doi.org/10.5281/zenodo.22905349

The link above is the **concept DOI**: it always resolves to the newest version. To pin
the exact weights behind a result, cite the version DOI instead; each release has its own,
listed on the record page. Version 5 is
[10.5281/zenodo.22921457](https://doi.org/10.5281/zenodo.22921457), version 4
[10.5281/zenodo.22921309](https://doi.org/10.5281/zenodo.22921309), version 3
[10.5281/zenodo.22912945](https://doi.org/10.5281/zenodo.22912945), version 2
[10.5281/zenodo.22905381](https://doi.org/10.5281/zenodo.22905381).

Trained on ground truth aligned against transcriptions by Yu. V. Konovalov, the
census1710 project and Rodnaya Vyatka.
