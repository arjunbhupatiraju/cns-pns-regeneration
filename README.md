CNS–PNS Regeneration Analysis

This repository contains a single-cell RNA sequencing analysis comparing injury responses in the central nervous system (CNS) and peripheral nervous system (PNS). The project investigates why peripheral nerves regenerate more effectively than central nervous system tissue and searches for transitional cell states and genes associated with regenerative versus chronic-injury outcomes.

Research question

Can single-cell trajectory analysis identify a plastic, fate-uncertain intermediate state that helps explain the difference between successful PNS regeneration and limited CNS repair?

Datasets

CNS: GSE172167

PNS: GSE198582

The raw datasets are not stored in this repository. Download them from NCBI GEO using the accession links above.

Analysis overview

The Colab notebook includes:

Quality control and preprocessing of CNS and PNS single-cell data

Dimensionality reduction, neighborhood graph construction, Leiden clustering, and PAGA analysis

CytoTRACE-based estimation of developmental plasticity

CellRank-based fate-probability and terminal-state analysis

Identification of high-entropy, approximately balanced fate-decision regions

Candidate gene analysis comparing alternative lineage outcomes

Robustness checks using multiple probability thresholds, sample support, neighborhood coherence, and permutation tests

Figure and processed-object export

Main tools

Python

Google Colab

Scanpy

AnnData

CellRank

CytoTRACE

NumPy, pandas, SciPy, and Matplotlib

Repository contents

*.ipynb — complete Google Colab analysis notebook

README.md — project description and usage instructions

Large raw datasets, intermediate AnnData objects, and generated outputs are excluded from GitHub because of file-size constraints.

How to run

Open the .ipynb file in GitHub.

Click Open in Colab if the button appears. Alternatively, download the notebook and upload it at Google Colab.

Download the two datasets from NCBI GEO.

Update the notebook's input and output paths to match your Google Drive folders.

Run the cells in order from the beginning.

Because single-cell analysis can be memory intensive, a Colab runtime with additional RAM may be necessary.

Reproducibility notes

Run the notebook sequentially because later cells depend on objects produced earlier.

Package versions are printed or installed within the notebook where applicable.

Random seeds and downsampling should be kept fixed when reproducing trajectory results.

Results may vary if package versions, preprocessing thresholds, or dataset annotations change.

Project status

This is an active research project. The analysis and interpretation may be revised as robustness testing and independent validation are completed.

Author

Arjun Bhupatiraju

License and data use

No license has currently been assigned to the code. The original datasets remain subject to the terms established by their respective authors and repositories.
