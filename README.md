# Single-Cell CNS–PNS Injury Analysis

This repository supports the manuscript:

**Single-Cell Analysis Reveals Shared Fate-Intermediate Architecture but Divergent Plasticity Across Peripheral and Central Nervous System Injury**

This study compares injury-responsive Schwann cells and microglia across three public mouse single-cell or single-nucleus RNA-sequencing datasets.

## Research question

Do the peripheral and central nervous systems contain comparable fate-intermediate cellular architectures, and are those states associated with different levels of transcriptional plasticity?

## Datasets

- **PNS discovery:** [GSE198582](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE198582)
- **CNS discovery:** [GSE172167](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE172167)
- **Independent CNS validation:** [GSE162610](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE162610)

The raw datasets are not stored in this repository. They can be downloaded from NCBI GEO using the accession links above.

## Analysis overview

The Google Colab notebook includes:

1. Quality control and preprocessing of all three datasets
2. PNS Schwann-cell discovery using CytoTRACE-informed CellRank modeling
3. CNS compartment screening and activated-microglia discovery analysis
4. Independent CNS validation using a sparse two-endpoint absorption model
5. Identification of probability-balanced, high-entropy intermediate populations
6. Sample- or library-stratified neighborhood-coherence permutation tests
7. Graph-connectedness and endpoint-distance analyses
8. Replicate-aware CytoTRACE comparisons
9. Cross-system comparison across 15,477 shared genes
10. Candidate evaluation for *Apoe*, *Gas7*, and *Timp2*
11. Main and supplementary figure generation

## Main tools

- Python
- Google Colab
- Scanpy
- AnnData
- CellRank
- CytoTRACE
- NumPy
- pandas
- SciPy
- igraph
- Matplotlib

## Repository contents

- `*.ipynb` — complete Google Colab analysis notebook
- `README.md` — project description and reproduction instructions

Large raw datasets and intermediate AnnData objects are excluded because they can be reconstructed from the cited GEO datasets.

## How to run

1. Open the `.ipynb` notebook on GitHub.
2. Select **Open in Colab**, or download the notebook and upload it to [Google Colab](https://colab.research.google.com/).
3. Download the three datasets from NCBI GEO.
4. Update the input and output paths to match your Google Drive folders.
5. Run the notebook cells sequentially from the beginning.

Single-cell analysis can require substantial memory. A high-RAM Colab runtime may be necessary.

## Reproducibility

- The analyses used random seed 42.
- The primary software environment contained:
  - Python 3.12
  - NumPy 2.4.6
  - SciPy 1.16.3
  - pandas 2.3.3
  - AnnData 0.13.2
  - Scanpy 1.12.2
  - CellRank 2.3.2
  - igraph 1.0.0
- Keep the random seed, downsampling procedures, endpoint orientation, and probability thresholds fixed when reproducing the reported results.
- Results may vary if package versions, dataset annotations, preprocessing thresholds, or random seeds are changed.

## Interpretation

The trajectory probabilities represent computational endpoint associations and not direct lineage observations. Schwann cells and microglia differ in developmental origin and biological function. Therefore, the analysis compares injury-responsive state architectures rather than asserting direct cellular equivalence.

## Project status

The discovery, robustness, independent-validation, and manuscript analyses are complete for the preprint version. Future versions may incorporate revisions made during peer review.

## Data and code availability

The source data are publicly available through GEO under GSE198582, GSE172167, and GSE162610. The analysis code is provided in this repository.

## Author

Arjun Bhupatiraju  
Independent Researcher  
Austin, Texas, United States

## Funding

No external funding supported this work.

## Competing interests

The author declares no relevant financial or non-financial competing interests.

## License and data use

No license has currently been assigned to the code. The original datasets remain subject to the terms established by their respective authors and repositories.
