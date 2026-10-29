# ood-alarms-real-shift
# I Tested 4 OOD Detectors on a Real Model Failure — the Baseline Won

Can a CNN know when an input is foreign? In my previous research, a model
that scored 93% on its European benchmark (EuroSAT) detected only 19% of
real Amazon deforestation — it labeled dense jungle as "SeaLake" with
confidence 0.83, and nothing ever warned that the inputs were out of
distribution (OOD). This study attaches four known OOD warning functions
to that same model and gives them a real exam: separate 4,050 home images
(the EuroSAT test split) from 289 foreign images (real Amazon patches),
with every grading rule fixed before the results were seen.

## Result

| alarm | separation | caught% |
|---|---|---|
| confidence | **0.911** | **54.0%** |
| mumble (energy) | 0.854 | 49.5% |
| center distance | 0.864 | 31.8% |
| knn distance | 0.775 | 19.4% |

- **The ranking inverted my written predictions almost perfectly.** I
  predicted knn best (0.89) and confidence weakest (0.55); reality placed
  confidence first and knn last. The predictions were committed in ink
  before grading, so the reversal is a measured result, not hindsight.
- **The verdict readers beat the map readers.** The alarms that read the
  model's scores won; the alarms that measure distances on its internal
  2,048-dimensional feature space lost — consistent with the idea that the
  network's own measurements hide the foreignness of foreign inputs.
- **The honest asterisk:** at a threshold that leaves 95% of home images
  alone, even the winning alarm catches only 54% of the foreign pile.
  No alarm here is deployment-ready; the exam measured how far from ready.

## The exam

Trained ResNet50 (93% on EuroSAT) → HOME pile = EuroSAT test split (4,050
images, same split seed as the original training — never trained on) vs
FOREIGN pile = 289 Sentinel-2 Amazon patches (foreign by construction) →
each alarm assigns every image one danger score → graded by separation
(probability a random foreign image out-scores a random home image) and
caught% (threshold set from the home pile only — no tuning on foreign data).

## Files

| File | What it is |
|---|---|
| `OOD_research.pdf` | Full paper (two-column) with figures |
| `notebook.ipynb` | All code: piles, signal collection, 4 alarms, grading, histograms |
| `eurosat_resnet50.pt` | The trained model under test |


## Honesty note

Predictions were written down before any grading ran. The threshold rule
and both grading metrics were fixed in advance. The center function is the
simple (non-shape-aware) version by design — this benchmarks what a
practitioner can cheaply attach to an existing model. AI assistance was
used for debugging, verification, and language editing; all experiments,
predictions, decisions, and findings are my own.

## Related

Part 3 of a research arc on benchmark-vs-reality collapse:
- Part 1 — tabular robustness study: [link to repo 1]
- Part 2 — the 93%→19% deforestation deployment study: [link to repo 2]

10.5281/zenodo.23778206
