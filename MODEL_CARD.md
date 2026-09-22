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
base_model: []
metrics:
  cer: 7.75
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
- **Base model** — *none: trained from scratch*
- **Architecture** — kraken's default recognition VGSL: 4 convolutional blocks, 3 bidirectional LSTM layers
- **Training** — AdamW, cosine schedule, batch 16, lr 3e-4; best epoch 12 of 40
- **Codec** — 273 symbols: pre-reform orthography with **ѣ ѳ ѵ ъ**, titlo abbreviations, superscript letters
- **Licence** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Published** — [10.5281/zenodo.22905349](https://doi.org/10.5281/zenodo.22905349), the concept DOI, which resolves to the newest version

Install it with `kraken get 10.5281/zenodo.22905350`, then run
`kraken -f image -x -i page.jpg out.xml segment -bl ocr -m skoropis-12.mlmodel`.

## Training data

Roughly **5300 annotated pages** of chancery and census books, dated **1625 to 1719**.
They come from the record-keeping of two Muscovite chanceries — the Siberian Chancery
and the Pomestny Chancery — and cover **the Urals, the Russian North and the Vyatka
land**: land-tax books, name books, census books and the tales of the first revision.
The hand is that of provincial scribes writing for Moscow, which is why a model trained
on it transfers across that whole territory rather than to one office.

The set is not fixed. It grows as more material is aligned, and a later release of this
model may rest on a different mixture within the same period and region.

Ground truth was made by aligning page images with transcriptions published by others —
**Yu. V. Konovalov**, the **census1710** project and **Rodnaya Vyatka** — with a smaller
part annotated directly. The model exists because those transcriptions do, and that is a
statement of provenance rather than of courtesy.

## Measured performance

- Best validation accuracy — **0.9225**
- Hand of 1680 — **91.5% of characters**
- Continuous text of 1620–1720 — **24–31 characters per line**
- Poskochin's Tobolsk census (RGADA f.214 op.5 d.261) — 43.6 chars/line at mean confidence 0.954
- First revision tales of 1719–1722 (f.214 op.1 d.1508) — 33.0 chars/line at 0.880
- **Cases of 1720–1800 — 13–16 chars/line, which is noise**

The `accuracy` field inside the file is empty: the training history was not preserved
on export, and putting a number there after the fact would pass one measurement off as
another. Every figure above is named together with the case it was measured on.

## Where it fails

**The hand gives out by the middle of the 18th century.** This is not a threshold to
tune: the script changes, and the model was trained on the 17th century. A book of 1747
returns 3.1 characters per line. For 1800–1870 use
`Kansallisarkisto/cyrillic-htr-model`; between 1720 and 1800 nothing works, and knowing
that beforehand is better than receiving plausible invention.

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
the exact weights behind a result, cite the version DOI instead — the first release is
[10.5281/zenodo.22905350](https://doi.org/10.5281/zenodo.22905350).

Trained on ground truth aligned against transcriptions by Yu. V. Konovalov, the
census1710 project and Rodnaya Vyatka.
