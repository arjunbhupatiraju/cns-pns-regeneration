# FateMultiplicity

Predictive multiplicity in cell-fate assignment, measured without labels.

Trajectory methods give each cell a probability of reaching each terminal fate, and those probabilities get read as biological commitment. This code asks a different question: if you re-run the analysis under every configuration the data cannot distinguish, does the cell keep the same fate?

Often it does not. On 6,000 microglia from injured mouse spinal cord, between 3.9% and 52.6% of cells have their assignment overturned by some reconstruction that fits the held-out data indistinguishably from the baseline — the range depends on how large a model space you search. Across those same reconstructions the median reported confidence varies by a factor of 483.

---

## The idea

Predictive multiplicity is measured over a **Rashomon set**: the models whose empirical risk falls within a tolerance of a reference model,

```
R(ε) = { θ : L(θ) ≤ L(θ*) + ε }
```

That definition needs labels, and it needs `L` to have a scale on which `ε` means something. Trajectory inference has neither. Cell fate is never observed, and these methods write down no likelihood over the expression matrix.

So the set is built a different way. Genes are split into a fitting set and a held-out set; a configuration is scored by how well the ordering it induces predicts the genes it never saw; and membership is decided by a statistical test rather than a tolerance:

```
R(α) = { θ : θ well-formed, and T(Δ(θ)) ≤ q_{1−α} },   Δ_g(θ) = d_g(θ) − d_g(θ*)
```

The non-inferiority margin inside `T` is not chosen. It is estimated from refits of the baseline that differ only in random seed, so it is measured in the data's own units. The tuning parameter is a significance level.

Over that set, the **certified fate margin** is the worst case a cell's decision margin takes:

```
FM_i(α) = inf_{θ ∈ R(α)} [ π_{i,k*}(θ) − max_{k≠k*} π_{i,k}(θ) ]
```

`FM > 0` means no admissible reconstruction overturns the assignment. `FM ≤ 0` means at least one does.

**What is new here is the set, not the margin.** The margin and the reading of its sign come from the predictive-multiplicity literature (Marx, Calmon & Ustun 2020; Watson-Daniels et al. 2023), and the same functional form appears in certified robustness. Cross-fitting and held-out prediction are standard. The contribution is replacing the tolerance ball with a test-calibrated acceptance region, which is what lets the machinery run where nothing is labelled.

---

## What the analysis found

| | |
|---|---|
| Held-out discrepancy discriminates orderings | z = −16.0 against a 200-permutation null |
| Prespecified controls | 5 of 5 behaved as predicted |
| FM predicts assignment error (simulation truth) | paired t = 2.85, p = 0.016, replicate as unit |
| Reported confidence across equivalent reconstructions | 483-fold range |
| Cells overturned, 24-configuration space | 3.9% |
| Cells overturned, 45-configuration space | 52.6% |
| Multiplicity at 1 dpi vs uninjured | 10.4% vs 0.4% |
| **Certification in simulations with no true fork** | **99.4% — the control that failed** |

That last row matters as much as the others. In data containing no bifurcation at all, every configuration partitions the cells the same arbitrary way, agreement is perfect, and FM reports determinacy. Certification means an assignment is *analytically determined*. It does not mean the fate structure exists.

---

## Installation

```bash
git clone https://github.com/arjunbhupatiraju/fatemultiplicity.git
cd fatemultiplicity
pip install -r requirements.txt
```

The `fatemult` modules need only numpy, scipy and scikit-learn. Running the full pipeline additionally needs scanpy, anndata, and the `fatestability` package from the earlier project for its method adapters.

---

## Layout

```
fatemult/
  partition.py           gene folds, co-expression modules, blocking power
  discrepancy_order.py   the von Neumann ratio estimator (in use)
  discrepancy.py         spline-deviance estimator (superseded, kept for the record)
  acceptance.py          R(α), FM, m-bar, Gate B, the headline comparison

notebooks/
  FateMultiplicity.ipynb the analysis, cells 1-18

contracts/               every contract version, v2.0.0 through v2.1.1
reports/                 gate reports, ledgers, validation output
figures/                 F1-F7 and S1-S9, and the code that draws them
manuscript/              LaTeX source for the paper and supplement
```

---

## Reproducing the analysis

Everything is driven by a frozen contract that is hashed before execution; downstream cells verify the hash before proceeding.

```python
from fatemult.partition import make_folds, detect_modules_auto
from fatemult.discrepancy_order import order_discrepancy
from fatemult.acceptance import (blocking_power_check,
                                 seed_calibrated_margin,
                                 build_rashomon_set_noninferiority,
                                 margins_over_set)

folds = make_folds(mean_expression, K=3, holdout_fraction=0.20, seed=20260904)
# ... fit each configuration on G1, score G2 with order_discrepancy ...

delta = seed_calibrated_margin(d_by_config, "theta_star",
                               seed_replicate_ids, module_labels)
R = build_rashomon_set_noninferiority(d_by_config, "theta_star",
                                      module_labels, delta=delta, alpha=0.05)
M = margins_over_set(pi_by_config, R.admitted, k_star)
print(f"{(M.fm <= 0).mean():.3f} of cells overturned")
```

`smoke_test.py` runs the whole construction on synthetic data with a known answer and checks four controls. Run it first; if the controls do not pass, nothing downstream is trustworthy.

The source data is GSE162610 (Milich et al. 2021), public on GEO. The `.h5ad` objects are not committed — they are large and the accession is open. Simulation objects are not committed either, because they are regenerated deterministically from master seed 20260808.

---

## Two gates

The pipeline will not proceed past either of these, by design.

**Gate A** asks whether held-out gene prediction can distinguish orderings at all. The baseline is compared to 200 permutations of its own pseudotime. If a scrambled ordering scores like a fitted one, the acceptance region is arbitrary and nothing downstream means anything.

**Gate B** asks whether R(α) is selective in both directions. Four configurations no analyst would defend must be kept out, and a refit differing only in random seed must be admitted. A test that rejects everything looks powerful and is useless; the specificity control is what separates the two cases.

---

## The amendment log

`contracts/` holds eight amended contract versions rather than one final file. Each records the observation and the number that motivated the change. Two of them revise a decision rather than a parameter:

- **v2.0.2** changed the Gate A criterion after five failures of an absolute threshold. The von Neumann ratio has a null pinned at 1, so a ">2× the null" criterion is arithmetically unreachable; it was replaced by a permutation test.
- **v2.0.4** changed the dataset. On the first object, an ordering fitted directly to the held-out genes — a circular procedure nothing legitimate can beat — reached only 0.936 against a null of 1.0. With that little dynamic range no discrepancy function could separate reconstructions there.

The log is kept in full rather than collapsed, because the amendments are the record of what did not work. `cell14_claim_map.json` maps every numerical claim in the paper to the file and contract hash it came from, and flags results that contradicted a prespecified prediction.

---

## Known limitations

The discrepancy is a proxy for scientific equivalence, not a measurement of it.

- It scores the **ordering**, so it is blind to a degenerate fate model. A configuration can order cells as well as the baseline while assigning p = 0.5 to 99% of them. The well-formedness screen removes such cases; the blindness remains.
- Its dynamic range on real data is narrow — the baseline sits at 0.992 against a null of 1.000 — which is why the gate uses a permutation test rather than a fixed threshold.
- On some datasets it cannot work at all. See the v2.0.4 amendment.

The α-sensitivity curve is flat from 0.001 to 0.5 in both model spaces. No configuration sits near the rejection boundary, so on this data the calibrated boundary makes no difference to the result. That is a limitation of the demonstration, not of the construction.

---

## Citing

```bibtex
@article{bhupatiraju_fatemultiplicity,
  title  = {FateMultiplicity: Predictive Multiplicity in Cell-Fate
            Assignment via Label-Free Rashomon Sets},
  author = {Bhupatiraju, Arjun},
  year   = {2026},
  note   = {Manuscript}
}
```

## Acknowledgment

An AI assistant (Claude, Anthropic) was used for analysis code development, methodological discussion, figure generation, and manuscript drafting and editing. The author designed the study, selected the datasets, made all final analytic decisions, and verified every reported result against the recorded evidence chain.
