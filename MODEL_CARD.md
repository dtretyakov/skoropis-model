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
  - '10.5281/zenodo.22921309'
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
offices, covering roughly **1620–1720** and reaching into the **1740s–60s**. No public model reads this hand as of
September 2026: the Slavic models in Transkribus are 16th-century semi-uncial and
11th–16th-century uncial, and the Russian ones begin in the 18th century. This one
fills that gap.

- **Format** — kraken 7, `.mlmodel`, `baselines` segmentation
- **Size** — 15 MB
- **Version** — 7, released September 2026: the weights of version 6 in a file kraken can load — version 6's file recorded its model type as `r` and is refused by `kraken ocr`; do not use it. The name stays `skoropis-12` across versions
- **Base model** — version 4 of this record, [10.5281/zenodo.22921309](https://doi.org/10.5281/zenodo.22921309); version 2 was trained from scratch
- **Architecture** — kraken's default recognition VGSL: 4 convolutional blocks, 3 bidirectional LSTM layers, 4.1 M parameters
- **Training** — fine-tuned from version 4, AdamW, cosine schedule, batch 16, lr 1.5e-4, augmentation, 8 epochs; the 1740s books repeated five times, the 1760s four, the 1620s–30s six; epoch 8 chosen on hand-read test lines, not on the internal validation score
- **Codec** — 279 symbols: pre-reform orthography with **ѣ ѳ ѵ ъ**, Cyrillic numerals, titlo abbreviations, superscript letters
- **Licence** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Published** — [10.5281/zenodo.22905349](https://doi.org/10.5281/zenodo.22905349), the concept DOI, which resolves to the newest version

Install it with `kraken get 10.5281/zenodo.22905349`, then run
`kraken -f image -x -i page.jpg out.xml segment -bl ocr -m skoropis-12.mlmodel`.

## Training data

About **14 500 annotated pages, 155 000 lines**, from **49 chancery, census and revision
books** dated **1621 to 1764**. They come from the record-keeping of the Siberian Chancery, the
Pomestny Chancery and the revision offices, and cover **the Urals, Western Siberia, the Kama
land, the Russian North, the Vyatka land and the Middle Volga**: land-tax books, name books,
census books and revision tales.

By period, distinct lines:

- **1620–1659** — about 65 400 lines (42 %)
- **1660–1699** — about 22 700 lines (15 %)
- **1700–1720** — about 53 200 lines (34 %)
- **1740s** — about 9 800 lines (6 %), repeated five times in training
- **1760s** — about 4 100 lines (3 %), repeated four times

About a third of the 1620–1659 lines come from microfilm rather than colour scans, as do
the 1630s test pages. The 1740s and 1760s pages write soul numbers, ages and years in Arabic
numerals, and the labels keep them so; lines whose number stands in a separate column the
line box does not reach were discarded rather than kept with a digit the image lacks.
The hand is that of provincial scribes writing for Moscow, which is why a model trained
on it transfers across that whole territory rather than to one office.

The set is not fixed. It grows as more material is aligned, and a later release of this
model may rest on a different mixture within the same period and region.

Editions normalise what the scribe wrote: ages and counts printed as Arabic numbers where
the page spells them out or writes Cyrillic numerals, years moved to the Christian era,
surnames in capitals. Before training, every
book was checked by eye on several pages and the labels were brought back to what the page
shows; lines whose alignment could not be trusted were discarded rather than kept.

Ground truth was made by aligning page images with transcriptions published by others —
**Yu. V. Konovalov**, the **census1710** project, **Rodnaya Vyatka** (I. S. Khudyakov) and
**A. G. Ushenin** (1670.ru) — with a smaller
part annotated directly. The model exists because those transcriptions do, and that is a
statement of provenance rather than of courtesy.

## Measured performance

Character accuracy of the weights of versions 6 and 7; version 4 (whose weights version 5 also carries) in brackets.

On lines read by hand, from books never shown to the model:

- **1740s** (42 lines, three books) — **61.2 %** (56.5 %)
- **1718–1720s** (87 lines, four books) — **89.8 %** (88.7 %)
- **1680s** (101 lines) — **92.7 %** (92.7 %)

On whole pages held out from books whose other pages were trained on — a measure of how
well the model learns one scribe, not a period:

- **1740s** (13 pages) — **88.3 %** (70.4 %)
- **1630s**, microfilm (36 pages) — **75.5 %** (70.3 %)

- Internal validation (every 20th page of most books, every 8th or 10th of the newer ones) — 0.9079

The `accuracy` field inside the file is empty: the training history was not preserved
on export, and putting a number there after the fact would pass one measurement off as
another. Every figure above is named together with the period it was measured on.

## Where it fails

**The hand weakens through the middle of the 18th century.** Chancery hands of the 1720s
still read well. In the 1740s–60s a hand the model has learned reads at nearly nine
characters in ten, but an unfamiliar one at about six: good for finding a formula, not for
reading a name. One of the three scribes in the 1740s test barely moved between versions
(47 → 49 %): that hand is in no transcription the model was trained on. For 1800–1870 use `Kansallisarkisto/cyrillic-htr-model`; between the 1740s
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
listed on the record page. Version 7 is
[10.5281/zenodo.22933516](https://doi.org/10.5281/zenodo.22933516); version 6
[10.5281/zenodo.22933378](https://doi.org/10.5281/zenodo.22933378) has a file kraken refuses; version 5
[10.5281/zenodo.22921457](https://doi.org/10.5281/zenodo.22921457), version 4
[10.5281/zenodo.22921309](https://doi.org/10.5281/zenodo.22921309), version 3
[10.5281/zenodo.22912945](https://doi.org/10.5281/zenodo.22912945), version 2
[10.5281/zenodo.22905381](https://doi.org/10.5281/zenodo.22905381).

Trained on ground truth aligned against transcriptions by Yu. V. Konovalov, the
census1710 project, Rodnaya Vyatka and A. G. Ushenin.
