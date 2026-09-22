# skoropis-12 — model, card and deposit workflow

Weights for a kraken recogniser of 17th-century Russian chancery cursive, the model
card that travels with them, and the workflow that deposits both on Zenodo.

**Published: [10.5281/zenodo.22905349](https://doi.org/10.5281/zenodo.22905349)** —
the concept DOI, always resolving to the newest version.

Install it with `kraken get 10.5281/zenodo.22905350`.

What the model reads, what it was trained on and where it fails is in
[MODEL_CARD.md](MODEL_CARD.md).

```
models/skoropis-12.mlmodel   weights, 15 MB, kraken 7, baselines segmentation
MODEL_CARD.md                model card with a YAML header valid against htrmopo v1
```

## Publishing

Set the secret once: **Settings → Secrets and variables → Actions → New repository
secret**, named `ZENODO_TOKEN`. Get the token at zenodo.org → Applications → Personal
access tokens, with the `deposit:write` and `deposit:actions` scopes.

Then **Actions → Publish to Zenodo → Run workflow**:

| input | what it does |
|---|---|
| `model` | the weights file; defaults to `models/skoropis-12.mlmodel` |
| `doi` | **leave empty for a first deposit**; give the previous version's DOI to release a new version |
| `listed` | whether to request community inclusion, which is what makes the model appear in `kraken list` |
| `author_name` | optional override of the author in the card; leave empty to use what the card already says |

The DOI appears in the run summary.

## Releasing a new version

Commit the new weights, adjust the card if needed, and run the same workflow with the
previous version's DOI in `doi`. The new version lands **beside** the old one rather
than replacing it: each version keeps its own DOI, and the record carries a concept DOI
that always resolves to the newest. Cite the concept DOI when the reference should
follow the model, a version DOI when a result must stay reproducible against exactly
the weights that produced it.

A deposit cannot be withdrawn, which is why the workflow runs on manual dispatch only
and never on push.

## What is not here

**The training data.** It rests on archival scans whose terms of access forbid
reproducing them. The weights are not images and carry nothing from a viewer, so the
model can be published where the pages behind it cannot.

**Checkpoints and intermediate weights.** Only what gets deposited lives here.
