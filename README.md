# FateMultiplicity

Predictive multiplicity in cell-fate assignment, measured without labels.

Trajectory methods give each cell a probability of reaching each terminal fate, and those probabilities get read as biological commitment. This code asks a different question: if you re-run the analysis under every configuration the data cannot distinguish, does the cell keep the same fate?

Often it does not. On 6,000 microglia from injured mouse spinal cord, 3.8% of cells have their assignment overturned by some reconstruction that fits the held-out data indistinguishably from the baseline. Add a second algorithm family and that rises to 20.4% — and the increase is not about searching harder. At matched model-space size it is 17.3% against 3.8%. Twelve configurations of one method expose more disagreement than twenty-four of another.

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
FM_i(α) = inf_{θ ∈ R(α)} [ p_{i,k*}(θ) − max_{k≠k*} p_{i,k}(θ) ]
```

`FM > 0` means every admitted model agrees on the fate call. `FM ≤ 0` means at least one admissible reconstruction overturns it.

---

## Does it beat just reading the probability?

That is the obvious objection, so it gets tested against four alternatives on simulation ground truth — including the two things a careful analyst would actually do instead.

```
                                       AUC
  FM (this work)                       0.812
  seed dispersion                      0.691
  m̄  (companion supremum)              0.614
  reported margin at θ*                0.577
  bootstrap stability                  0.465   ← below chance
```

Bootstrap stability is the standard heuristic: refit on subsamples, count how often a cell's call flips. Here it is worse than uninformative, because subsampling re-roots the walk and the flips track topology rather than per-cell uncertainty.

The ranking alone proves nothing — those intervals overlap. The nested test is the comparison that does. With each alternative already in a cluster-robust regression, FM stays significant in every case (`p` between 1.5×10⁻⁴ and 9.2×10⁻³) while the alternative's own coefficient never does.

Seed dispersion is the one that mattered most. The acceptance margin is estimated from exactly that variation, so if reseeding predicted misassignment as well as FM, the whole construction would be unnecessary. It does not, and it adds nothing once FM is in the model.

---

## The control that failed

On simulated topologies containing **no branch point at all**, the framework certifies over 99% of cells.

That is a prespecified negative control, it is reported in the abstract, and it is the most useful thing in the paper. An algorithm class sharing an inductive bias toward discrete terminal states imposes the same partition on continuous data. So agreement across a Rashomon set measures algorithmic determinacy, not biological structure — and low multiplicity is not evidence that a bifurcation exists.

---

## What is here

```
FateMultiplicity.ipynb          the analysis in the manuscript
FateMultiplicityPrework.ipynb   dataset selection, discrepancy development,
                                and the metrics that were tried and dropped
FateStability.ipynb             earlier trajectory audit; not part of this
                                work, but the simulation cohort and the
                                perturbation axes come from it
Paper.ipynb                     earlier PNS/CNS injury study; supplies the
Additional_Validation.ipynb     object used for one ceiling test
```

`FateMultiplicity.ipynb` emits the versioned contract, the gene-partition and module registry, the per-configuration admission ledger, and the environment lock.

---

## Data

Public, and downloaded by the notebook rather than stored here.

```
GSE162610    mouse spinal cord injury, 6,000 microglia after subsampling
GSE72857     mouse myeloid progenitors, 2,730 cells
```

The 75 simulation objects are regenerated deterministically from master seed `20260808` rather than archived as matrices — five topology scenarios, three difficulty levels, five replicates each.

---

## Running it

Built in Colab with a Drive mount. Elsewhere, point `BASE` at a writable directory and the rest follows.

```bash
pip install anndata scanpy "pandas>=2.3,<3" palantir==1.4.5 cellrank==2.3.2
```

Fitting is checkpointed per configuration, so an interrupted run resumes instead of restarting. A cold run takes several hours, most of it the two-factor grid — which is reported for completeness, not used for the headline numbers.

---

## Why you can check this

Every analytic choice is fixed in a versioned contract before execution and hashed. Downstream stages verify the hash, so a parameter cannot move mid-analysis without invalidating the record.

Eight amendments are logged, each with the observation and the number that prompted it. Two revise a decision rather than a parameter: the acceptance criterion, after an absolute threshold proved unreachable for a statistic with a bounded null; and the dataset, after a preliminary object turned out to lack the dynamic range to separate configurations. The whole sequence is kept, not just the final state — the intermediate states are the record of what did not work.

Five negative controls were registered with predicted outcomes before execution. Configurations that fail to fit stay in every denominator and are never quietly recoded as poor fits. The notebook emits a map tying each numerical claim in the manuscript to the stage and contract hash that produced it, and flags anything that contradicted a prespecified prediction.

---

## What it cannot do

- Validation is against simulation, not clonal lineage tracing. The empirical datasets have no ground-truth fate labels; that is the whole reason this construction exists.
- The acceptance boundary is unexercised at the published block count. Across two datasets and 51 configurations, sweeping α over three orders of magnitude changes nothing. That turns out to be a resolution limit rather than a property of the construction — at finer blocking the boundary does act — but it is a limitation of the reported analysis.
- Certification exceeds 99% on unbranched topologies. See above; the method cannot detect a bias its whole model class shares.
- The model space is a discrete grid of configurations an analyst would plausibly choose, not a continuous region.

---

## Author

Arjun Bhupatiraju · McNeil High School, Austin, TX
ORCID [0009-0006-2667-0098](https://orcid.org/0009-0006-2667-0098)

An AI assistant (Claude, Anthropic) was used for analysis code development, methodological discussion, figure generation, and editorial review. The author conceived the study, designed the analytic protocol and its gates, selected the datasets, made all analytic decisions, verified every reported result against the recorded evidence chain, and accepts responsibility for the content.

A manuscript describing this work is under review.
