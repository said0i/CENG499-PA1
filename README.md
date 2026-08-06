# CENG499 — PA1: Decision Trees and Pruning

Introduction to Machine Learning (METU CENG499), Spring 2025.

A decision tree built from scratch in NumPy (Gini splitting, majority-class leaves), with reduced
error pruning and a custom statistical pruning method, evaluated on the Wine dataset.

## Contents

| File | Description |
| --- | --- |
| `PA1.ipynb` | Implementation and experiments (Tasks 1–6) |
| `report.md` | Report — results and discussion |
| `report1.pdf` | The same report, as submitted |
| `Ceng499___PA1.pdf` | Assignment specification |
| `*.png` | EDA figures used by the report |

Data comes from `sklearn.datasets.load_wine()`, so nothing needs downloading. Requires `numpy`,
`pandas`, `matplotlib`, `seaborn`, `scipy` and `scikit-learn` (for the train/test split only).

**Findings are in [`report.md`](report.md).**

## Known issue

The notebook and report are kept as submitted. Reviewing the code afterwards, the reduced error
pruning in Task 4 does not work as intended:

- `rec_predict` takes a `node_checking` argument to simulate collapsing a candidate node, but the
  two recursive calls do not forward it, so it resets to its default below the root. Simulating the
  collapse of any non-root node is therefore a no-op and returns exactly the unpruned accuracy.
- Because the simulated accuracy always equals the real accuracy, the `pruned_accuracy >= accuracy`
  test is always true and nodes are collapsed unconditionally — the validation set never actually
  influences the decision.
- Node ids are assigned by reading `internal_node_count` after the children are built, so they are
  not unique (roughly 17 distinct ids across 62 nodes). The nodes that share the root's id are the
  only ones spared, which is why the tree shrinks to a shallow spine instead of a single leaf.

The reported numbers are genuine outputs of this code, but the report attributes the weak pruning
results to the small size of the Wine dataset, whereas the real cause is that pruning was never
gated on validation accuracy. Additionally, the Task 6 loop reuses the same already-pruned tree
across the three α values, so the custom pruning columns are cumulative rather than independent.
