# FateStability

**Failure-aware robustness assessment for single-cell trajectory and fate inference across peripheral and central nervous system injury**

This repository supports the manuscript:

> **FateStability Reveals Stable Trajectory Ordering but Model-Dependent Fate Assignment Across Peripheral and Central Nervous System Injury**

The study combines biological analysis of injury-responsive Schwann cells and microglia with a controlled computational benchmark of trajectory and fate-inference stability.

## Overview

Single-cell trajectory methods can produce visually coherent pseudotime paths and apparently decisive fate probabilities. Those outputs are nevertheless sensitive to preprocessing, graph construction, directional priors, root and terminal definitions, state resolution, subsampling, random seed, and inference method.

We introduce **FateStability**, a purpose-built, frozen, failure-aware computational framework for testing whether trajectory-derived scientific conclusions survive those analytical changes. FateStability is not a replacement trajectory-inference solver. Its original contribution is an integrated evaluation methodology that separates:

- pseudotime ordering;
- fate-probability assignment;
- probability calibration;
- inferred topology;
- localization of probability-balanced regions;
- exact cell identity; and
- technical failure and operational coverage.

The framework retains failed and inapplicable configurations in the audit record rather than silently restricting conclusions to successful runs.

## Research questions

1. Can trajectory ordering remain reproducible while cell-level fate probabilities disagree?
2. How stable are inferred fate assignments under controlled analytical perturbations?
3. Are high-confidence fate probabilities numerically calibrated?
4. Do probability-balanced regions recur geometrically while selecting different cells?
5. Which conclusions remain supportable after technical failures, negative controls, and real-data validation are included?

## Datasets

Three public mouse single-cell or single-nucleus RNA-sequencing datasets were analyzed independently:

| Role | Biological focus | Accession |
|---|---|---|
| PNS discovery | Injured sciatic-nerve Schwann cells | [GSE198582](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE198582) |
| CNS discovery | Spinal-cord injury nuclei | [GSE172167](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE172167) |
| Independent CNS validation | Lesion-centered spinal-cord injury cells | [GSE162610](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE162610) |

The raw datasets are not stored in this repository. They remain available from NCBI GEO through the accession links above.

## FateStability benchmark

### Simulated trajectory scenarios

The frozen simulation registry contains five scenarios:

1. `clean_bifurcation`
2. `overlapping_bifurcation`
3. `continuous_nonbranching`
4. `imbalanced_rare_branch`
5. `confounded_pseudobranch`

Each scenario was calibrated across easy, moderate, and hard settings before the full benchmark was released.

### Inference methods

- CellRank
- Palantir
- Absorbing random walk

CellRank and Palantir are established external methods. The absorbing-walk implementation provides an additional graph-based comparator. FateStability contributes the shared interface, frozen perturbation design, evaluation registry, failure taxonomy, calibration analysis, and evidence-synthesis workflow.

### Prespecified perturbation axes

- balanced-probability band;
- number of highly variable genes;
- number of macrostates;
- number of neighbors;
- root definition;
- inference seed;
- subsample fraction; and
- terminal definition.

Method-specific axes that did not apply to a particular method were retained as explicitly inapplicable rather than treated as completed runs.

## Main benchmark results

- **3,825** method-configuration rows were registered.
- **1,875** method-specific or otherwise inapplicable rows were retained as prespecified skips.
- **1,950** runs were eligible for inference.
- **1,788** eligible runs completed with valid outputs (**91.7%**).
- **162** technical failures were retained in the benchmark denominator.
- **3,600** perturbation comparisons were planned and **1,608** valid matched pairs were analyzed.
- **1,091** valid branching runs contributed to calibration analysis.
- **766** matched cross-method comparisons were available.
- Median pseudotime agreement between Palantir and absorbing walk was positive (**Spearman rho = 0.831**), whereas matched fate-probability correlation was strongly negative (**median = -0.712**).
- Median expected calibration error was **0.263** for Palantir and **0.315** for absorbing walk.
- Median Brier score was **0.219** for Palantir and **0.249** for absorbing walk.

These results support a qualified methodological conclusion: agreement in trajectory ordering does not imply agreement or calibration in cell-level fate assignment.

## Real-data application

The reduced FateStability audit evaluated three biological dataset roles with 27 essential registered configurations:

- **24/27** runs completed;
- all three CellRank real-data baselines failed technically in the available computational environment; and
- real-data cross-method interpretation was therefore restricted to Palantir and absorbing walk.

The PNS reconstruction provided an important identity-level result: the original and independently reconstructed 40–60% probability bands recurred geometrically but shared no cells in the matched 318-cell cohort (**Jaccard = 0**). This demonstrates why a recurrent region in model space should not automatically be interpreted as a stable biological population.

## Negative controls and limitations

The negative-control analysis did not produce valid run-level topology denominators. Of 1,530 planned negative-control runs, 780 were technical failures and 750 were indeterminate under the locked topology criteria. Consequently, this execution does **not** provide a defensible estimate of false-bifurcation specificity.

Additional limitations include:

- calibration and stability estimates are conditional on successful outputs;
- real-data CellRank comparison was incomplete;
- biological replicate identifiers were not recoverable in a common form across the final synthesis;
- PNS and CNS datasets differ in lineage, anatomy, modality, sample size, injury design, and source-study processing; and
- the study is computational and does not directly establish lineage history, regenerative function, or causal gene regulation.

These limitations are retained as scientific results rather than removed from the final analysis.

## Notebook organization

The authoritative Google Colab notebook contains 20 cells covering:

1. frozen project contract and directory initialization;
2. environment installation and compatibility validation;
3. utility, schema, cache, logging, and figure tests;
4. artifact discovery and input audit;
5. canonical real-data selection and readiness checks;
6. count-based trajectory simulation engine;
7. scenario calibration and truth validation;
8. frozen simulation-cohort generation;
9. evaluation metrics and known-answer tests;
10. unified preprocessing and inference interface;
11. CellRank adapter and pilot validation;
12. Palantir adapter and pilot validation;
13. absorbing-random-walk adapter and pilot validation;
14. frozen multi-method benchmark;
15. perturbation-level fate-stability analysis;
16. false-bifurcation and negative-control analysis;
17. uncertainty calibration, cross-method agreement, and failure analysis;
18. reduced real PNS/CNS application;
19. final statistical synthesis and publication outputs; and
20. reproducibility audit and release packaging.

## Repository contents

- `FateStability.ipynb` — authoritative 20-cell Google Colab workflow
- `README.md` — project overview and reproduction guidance
- Release manifests, registries, requirements, figures, and tables when included in the repository release

Large GEO matrices and intermediate AnnData objects are excluded. The notebook records their expected source paths, hashes, roles, and reconstruction logic.

## Reproduction

1. Clone or download this repository.
2. Open `FateStability.ipynb` in [Google Colab](https://colab.research.google.com/).
3. Mount Google Drive when prompted.
4. Download the three public GEO datasets and place them in the paths expected by the notebook, or update the path configuration before freezing a new contract.
5. Run the notebook cells sequentially.
6. Do not modify the frozen contract, scenario registry, evaluation registry, seeds, or run matrices when reproducing the reported release.
7. Use the saved-output and cache-validation paths when reconnecting a Colab runtime; do not silently regenerate completed runs under different package versions.

The complete benchmark is computationally intensive. A high-memory environment is recommended for large real-data CellRank runs. The notebook records resource exclusions separately from inferential failures.

## Reproducibility controls

- FateStability master seed: `20260808`
- Frozen contract SHA-256: `0f9d22772e3a7e31d44e4528cd23927af593725179ce8f65ab60656e524591e4`
- Frozen simulation-scenario registry SHA-256: `692f929cc5b7082390d37ba08064cdfc3f6d447fe9902280f7622cf4d32c154e`
- Versioned environment and requirements files are generated by the notebook.
- Source artifacts are hashed before and after relevant stages.
- Cache entries are reused only after configuration and checksum validation.
- Simulation truth is stored separately from primary method inputs.
- Structured failures remain in denominators and release manifests.

The audited environment used Python 3.12 and included AnnData 0.13.2, Scanpy 1.12.3, CellRank 2.3.2, Palantir 1.4.5, NumPy 2.4.6, pandas 3.0.5, SciPy 1.18.0, and scikit-learn 1.9.0. Consult the generated locked requirements for the complete environment rather than installing only this abbreviated list.

## Interpretation

Trajectory probabilities are fitted associations with computational endpoints, not direct observations of lineage commitment. Probability-balanced regions are therefore treated as model-localized regions for further investigation rather than proof of stable intermediates or shared CNS–PNS regenerative mechanisms.

The central FateStability recommendation is to report trajectory ordering, topology, fate probability, calibration, selected-cell identity, and technical failure as separate quantities. Agreement in one quantity should not be used as evidence of agreement in another.

## Manuscript and citation

The repository supports:

> Arjun Bhupatiraju. **FateStability Reveals Stable Trajectory Ordering but Model-Dependent Fate Assignment Across Peripheral and Central Nervous System Injury.** Preprint manuscript, 2026.

Update this section with the bioRxiv DOI after the preprint is posted.

## Data and code availability

Source expression data are publicly available through GEO under GSE198582, GSE172167, and GSE162610. The notebook, computational framework, and documented release materials are provided through this repository. Large source matrices and intermediate analysis objects should be reconstructed from the cited records and frozen manifests.

## Author

**Arjun Bhupatiraju**  
Austin, Texas, United States

## Funding

No external funding supported this work.

## Competing interests

The author declares no relevant financial or non-financial competing interests.

## Generative AI disclosure

Generative AI tools supported language editing, document formatting, and software troubleshooting. The author reviewed the analyses, interpretations, and final content and accepts responsibility for the work.

## License and data use

No open-source license has currently been assigned to the code. Public availability of the repository does not by itself grant permission to reuse, modify, or redistribute the code. The original datasets remain subject to the terms established by their respective authors and repositories.
