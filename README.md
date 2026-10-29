# Exploring the Oral Microbiome for Biomarker Discovery in Early Cancer Detection

A 16S rRNA-based analysis integrating bioinformatics, microbial diversity, differential-abundance testing, and machine learning to identify candidate microbial biomarkers associated with oral precancer.

## Project Overview

This project investigates salivary microbiome differences between Healthy and Precancer groups using publicly available 16S rRNA sequencing data from **251 saliva samples (127 Healthy and 124 Precancer)**.

The workflow combines QIIME 2 and DADA2 for sequence processing, microbial diversity and taxonomic analysis, ANCOM-BC2 for differential-abundance testing, and machine learning for sample classification. Candidate biomarkers are prioritized by integrating statistical findings with model-selected features.

The study focuses on **Healthy–Precancer classification and exploratory biomarker discovery**, not the diagnosis of established oral cancer or prediction of cancer progression.

## Objectives

- Process 16S rRNA sequencing reads to identify amplicon sequence variants (ASVs).
- Compare microbial diversity and taxonomic composition between Healthy and Precancer samples.
- Identify differentially abundant ASVs associated with oral precancer.
- Develop and evaluate machine-learning models for Healthy–Precancer classification.
- Integrate statistical and model-selected features to prioritize candidate microbial biomarkers.
- Visualize microbial patterns, model performance, and candidate biomarkers.

## Dataset

**Source:** [NCBI BioProject PRJNA386665](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA386665)

| Characteristic | Value |
|---|---|
| Total saliva samples | 251 |
| Healthy | 127 |
| Precancer | 124 |
| Initial DADA2 ASVs | 12,932 |
| ASVs retained for machine learning | 823 |
| Classification labels | Healthy (0), Precancer (1) |

Samples with established oral squamous cell carcinoma (OSCC) were excluded. The initial ASV count represents the feature table before prevalence filtering.

## Analysis Workflow

### 1. Data Acquisition and Sequence Processing

Sequencing data were retrieved using the SRA Toolkit (`prefetch`, `fasterq-dump`) and imported into QIIME 2.

Single-end reads were processed using DADA2 for quality filtering, denoising, and chimera removal (`trim-left = 0`, `trunc-len = 240`), generating an ASV feature table and representative sequences.

### 2. Taxonomic and Phylogenetic Analysis

Taxonomy was assigned using a Naive Bayes classifier trained on dereplicated SILVA 138.2 V4 reference sequences extracted with F515/R806 primers. Classification used QIIME 2's `feature-classifier classify-sklearn`.

Phylogenetic analysis used MAFFT and FastTree. Microbial composition was examined at phylum and genus levels.

### 3. Microbial Diversity Analysis

**Alpha diversity:** Shannon, Observed Features, and Faith's Phylogenetic Diversity.

**Beta diversity:** Bray–Curtis, Jaccard, weighted UniFrac, and unweighted UniFrac.

QIIME 2 generated diversity metrics, distance matrices, and PCoA results. Shannon and Observed Features were compared using Mann–Whitney U tests. Bray–Curtis PCoA, PERMANOVA, and PERMDISP were used to assess community-level differences. A cluster-exclusion sensitivity analysis examined the robustness of the PERMANOVA finding.

### 4. Differential-Abundance Analysis

ANCOM-BC2 identified ASVs differing significantly between Healthy and Precancer groups using an adjusted p-value threshold of **q < 0.05**.

### 5. Machine-Learning Classification

Four classifiers were evaluated: Dummy Classifier, Decision Tree, Random Forest, and Elastic Net Logistic Regression.

The workflow included:

- An 80:20 stratified split (200 training, 51 test samples).
- A 10% prevalence threshold, retaining 823 ASVs.
- Centered log-ratio (CLR) transformation with pseudocount 0.5.
- Standard scaling for Elastic Net.
- Repeated stratified five-fold cross-validation (10 repetitions).
- Held-out evaluation using ROC-AUC, accuracy, precision, sensitivity, specificity, F1-score, and a confusion matrix.
- A 95% bootstrap ROC-AUC confidence interval from 1,000 resamples.

### 6. Candidate Biomarker Prioritization

Candidate ASVs were identified by intersecting ANCOM-BC2-significant features with ASVs selected by Elastic Net and checking directional agreement.

| Selection stage | ASVs |
|---|---:|
| ANCOM-BC2-significant | 376 |
| Elastic Net-selected | 103 |
| Overlapping | 52 |
| Directionally concordant candidates | **39** |

The 39 candidates were taxonomically annotated and visualized. This process represents exploratory prioritization rather than independent validation.

### 7. Visualization

The analysis includes alpha-diversity plots, Bray–Curtis PCoA, phylum- and genus-level relative abundance plots, ROC curves, a confusion matrix, Elastic Net coefficient and ANCOM-BC2 effect-size plots, and a candidate biomarker heatmap using log-transformed, row-wise standardized relative abundances.

## Results

### Microbial Diversity and Composition

Shannon diversity (p = 0.2021) and Observed Features (p = 0.7247) showed no significant differences between groups.

Bray–Curtis PERMANOVA detected a statistically significant but small difference in microbial composition (pseudo-F = 4.4918, R² = 1.77%, p = 0.001). PERMDISP was nonsignificant (p = 0.974). Cluster-exclusion analysis supported this finding (R² = 1.94%, p = 0.001; n = 223).

### Differential Abundance

ANCOM-BC2 identified **376 significant ASVs**, including 116 enriched and 260 depleted in Precancer samples. Significant ASVs included assignments to *Prevotella*, *Veillonella*, *Streptococcus*, and *Neisseria*. These results concern individual ASVs, not entire genera.

### Machine-Learning Performance

| Model | Mean CV ROC-AUC |
|---|---:|
| Elastic Net Logistic Regression | **0.843** |
| Random Forest | 0.815 |
| Decision Tree | 0.713 |
| Dummy Classifier | 0.507 |

**Elastic Net held-out test performance:**

| Metric | Result |
|---|---:|
| ROC-AUC | **0.8385** |
| 95% bootstrap CI | 0.7084–0.9348 |
| Accuracy | 74.51% |
| Sensitivity | 80.00% |
| Specificity | 69.23% |
| Precision | 71.43% |
| F1-score | 0.7547 |

The combined statistical and machine-learning analysis prioritized **39 exploratory candidate ASVs** for further validation.

## Tools and Technologies

**Bioinformatics:** SRA Toolkit, QIIME 2, DADA2, SILVA 138.2, MAFFT, FastTree, ANCOM-BC2.

**Python analysis:** Jupyter Notebook/JupyterLab, NumPy, pandas, SciPy, scikit-learn, Matplotlib, Seaborn.

## Repository Structure

```text
Oral-Microbiome-Biomarker-Discovery/
├── README.md
├── requirements.txt
└── oral_microbiome_analysis.ipynb
```

Additional input files, exported QIIME 2 results, figures, and report documents may be included separately.

## Installation and Usage

Install downstream Python dependencies:

```bash
python -m pip install -r requirements.txt
```

Open `oral_microbiome_analysis.ipynb` in Jupyter Notebook or JupyterLab.

**Required notebook inputs:**

```text
feature-table.tsv
core_metadata_healthy_vs_precancer.tsv
taxonomy.qza
ancombc2-significant-taxa.tsv
ordination.txt
shannon.tsv
observed_features.tsv
```

Some inputs must be exported from QIIME 2 artifacts before running the relevant notebook sections. Place the required files in the notebook's working directory or update their paths.

**Generated outputs include:**

```text
train_sample_ids.csv
test_sample_ids.csv
Final_Candidate_Biomarkers.csv
```

**Environment note:** `requirements.txt` covers downstream Python dependencies. QIIME 2, DADA2, SRA Toolkit, and ANCOM-BC2 require separate installations or environments.

## Limitations and Future Work

- The study used a single cohort of 251 samples without external validation.
- Potential clinical confounders were not fully controlled, and 16S rRNA sequencing limited taxonomic resolution.
- The 39 candidate ASVs are exploratory and require independent validation.
- Future work should evaluate larger, independent cohorts, address batch effects, and investigate smaller biomarker panels.

## Conclusion

This study identified a statistically significant but small difference in salivary microbial composition between Healthy and Precancer groups. Elastic Net achieved a held-out ROC-AUC of **0.8385**, while integration of ANCOM-BC2 and model-selected features prioritized **39 directionally concordant ASVs** as exploratory biomarker candidates.

These findings support further investigation of salivary microbial signatures for oral precancer detection, subject to independent validation.
