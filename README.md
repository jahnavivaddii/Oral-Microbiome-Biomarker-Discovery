# Exploring the Oral Microbiome for Biomarker Discovery in Early Cancer Detection

An integrated 16S rRNA microbiome analysis combining bioinformatics, microbial diversity analysis, differential abundance testing, and machine learning to investigate differences between Healthy and Precancer oral microbiome samples.

## Project Overview

This project explores the oral microbiome for potential biomarker discovery in early cancer detection, with a focus on research relevant to Oral Squamous Cell Carcinoma (OSCC).

Using 16S rRNA sequencing data from 251 samples (127 Healthy and 124 Precancer), the study investigates differences in microbial diversity, taxonomic composition, and microbial abundance between healthy and precancerous groups. The workflow integrates QIIME 2 and DADA2 for sequencing data processing, microbial community analysis, differential abundance analysis using ANCOM-BC2, and machine learning for sample classification.

The primary objective is to characterize microbial differences between Healthy and Precancer samples and identify candidate microbial biomarkers by integrating statistical findings with machine learning results.

## Objectives

- Process 16S rRNA sequencing data to generate amplicon sequence variants (ASVs) for downstream microbiome analysis.
- Assess and compare alpha and beta diversity between Healthy and Precancer samples.
- Characterize microbial community composition and investigate taxonomic differences between the two groups.
- Identify differentially abundant microbial features associated with precancerous conditions.
- Develop and evaluate machine learning models for Healthy versus Precancer classification.
- Integrate differential abundance results with machine learning feature selection to identify candidate microbial biomarkers.
- Visualize microbial diversity, taxonomic composition, classification performance, and candidate biomarker patterns.

## Dataset

This study uses publicly available 16S rRNA sequencing data obtained from the NCBI Sequence Read Archive (SRA), associated with **BioProject PRJNA386665**.

**Data source:** [NCBI BioProject PRJNA386665](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA386665)

| Characteristic | Value |
|---|---|
| NCBI BioProject accession | PRJNA386665 |
| Total samples analyzed | 251 |
| Healthy samples | 127 |
| Precancer samples | 124 |
| Initial amplicon sequence variants (ASVs) | 12,932 |
| Classification target | Healthy (0) vs Precancer (1) |

Raw sequencing reads were processed using QIIME 2 and DADA2 to generate an ASV feature table and representative sequences. The initial ASV count refers to the feature table before downstream prevalence-based filtering.

## Analysis Workflow

The project consists of two main stages: upstream bioinformatics processing using QIIME 2 and downstream statistical analysis and machine learning using Python.

### 1. Raw Sequencing Data Acquisition

Raw sequencing data were retrieved from NCBI SRA using the SRA Toolkit.

- Downloaded sequencing data using `prefetch`.
- Converted SRA files to FASTQ format using `fasterq-dump`.
- Prepared sample metadata for Healthy and Precancer group comparisons.

### 2. QIIME 2 Processing and DADA2 Denoising

Single-end sequencing reads were imported into QIIME 2 using `qiime tools import`.

- Inspected sequencing quality using `qiime demux summarize`.
- Performed denoising using `qiime dada2 denoise-single`.
- Generated an ASV feature table, representative sequences, and denoising statistics.

### 3. Taxonomic Classification

Taxonomic classification was performed using the **SILVA 138.2** reference database.

The upstream workflow included V4-region reference extraction, reference dereplication, Naive Bayes classifier training, and taxonomic assignment using `qiime feature-classifier classify-sklearn`.

Taxonomic composition was explored using QIIME 2 taxa bar plots and downstream Python visualizations.

### 4. Phylogenetic and Diversity Analysis

A phylogenetic tree was generated using the QIIME 2 MAFFT/FastTree workflow.

**Alpha diversity metrics**

- Shannon diversity
- Observed Features
- Faith's Phylogenetic Diversity

**Beta diversity metrics**

- Bray–Curtis dissimilarity
- Jaccard distance
- Weighted UniFrac
- Unweighted UniFrac

The upstream workflow generated diversity artifacts and ordination results for these metrics.

In the Python notebook, Shannon diversity and Observed ASVs were compared between groups using Mann–Whitney U tests. Bray–Curtis PCoA was used to visualize community differences, with PERMANOVA and PERMDISP applied to assess group differences and dispersion.

### 5. Taxonomic Composition Analysis

Microbial community composition was examined using taxonomic annotations and ASV abundance data.

Relative abundance visualizations were generated at the phylum and genus levels to investigate taxonomic patterns across Healthy and Precancer samples.

### 6. Differential Abundance Analysis

ANCOM-BC2 was used to identify microbial features showing statistically significant differences between Healthy and Precancer samples.

The resulting significant-feature table was used for exploratory candidate biomarker identification.

### 7. Machine Learning Classification

Four classification approaches were compared:

- Dummy Classifier (baseline)
- Elastic-Net Logistic Regression
- Random Forest
- Decision Tree

The machine learning pipeline included:

- An 80/20 stratified training and test split.
- Prevalence-based feature filtering using a 10% threshold.
- Centered log-ratio (CLR) transformation with a pseudocount of 0.5.
- Feature standardization for Elastic-Net Logistic Regression.
- Repeated stratified five-fold cross-validation with 10 repeats.
- Held-out test evaluation of the selected Elastic-Net model.

Model performance was assessed using classification metrics, a confusion matrix, and ROC-AUC. A bootstrap confidence interval was also estimated for the held-out test ROC-AUC.

### 8. Exploratory Candidate Biomarker Identification

Candidate microbial features were identified by integrating:

1. ASVs with nonzero coefficients in the fitted Elastic-Net classifier.
2. Statistically significant ASVs identified by ANCOM-BC2.

Overlapping features were assessed for directional agreement between their Elastic-Net coefficients and ANCOM-BC2 results.

The resulting candidates were taxonomically annotated and visualized.

### 9. Data Visualization

The Python notebook includes:

- Alpha-diversity comparison plots.
- Bray–Curtis PCoA visualization.
- Phylum- and genus-level relative abundance plots.
- ROC curve and confusion matrix.
- Elastic-Net coefficient and ANCOM-BC2 effect-size plots.
- Candidate biomarker heatmap using log-transformed, row-wise standardized relative abundance values.

## Results

The selected Elastic-Net classifier produced the following recorded held-out test results:

| Metric | Result |
|---|---|
| Accuracy | 74.51% |
| Sensitivity | 80.00% |
| Specificity | 69.23% |
| ROC-AUC | 0.8385 |
| 95% bootstrap ROC-AUC confidence interval | 0.7084–0.9348 |

The integration of Elastic-Net feature selection and ANCOM-BC2 differential abundance results identified **39 concordant candidate ASVs**.

These findings describe associations and classification performance within the analyzed dataset. The candidate biomarkers have not been independently or clinically validated.

## Tools and Technologies

**Bioinformatics**

- NCBI SRA Toolkit
- QIIME 2
- DADA2
- SILVA 138.2
- MAFFT
- FastTree
- ANCOM-BC2

**Statistical Analysis and Machine Learning**

- Python
- Jupyter Notebook
- NumPy
- Pandas
- SciPy
- Scikit-learn
- Matplotlib
- Seaborn

## Repository Structure

```text
Oral-Microbiome-Biomarker-Discovery/
├── README.md
├── requirements.txt
└── oral_microbiome_analysis.ipynb
```

Additional documentation, figures, and the final scientific report may be added as the project is finalized.

## Installation and Usage

Install the downstream Python dependencies:

```bash
python -m pip install -r requirements.txt
```

Open `oral_microbiome_analysis.ipynb` in Jupyter Notebook or JupyterLab.

The notebook requires the following input files:

```text
feature-table.tsv
core_metadata_healthy_vs_precancer.tsv
taxonomy.qza
ancombc2-significant-taxa.tsv
ordination.txt
shannon.tsv
observed_features.tsv
```

Place these files in the notebook's working directory or update the file paths accordingly.

The notebook also generates the following output files:

- `train_sample_ids.csv`
- `test_sample_ids.csv`
- `Final_Candidate_Biomarkers.csv`

**Note:** `requirements.txt` contains the Python dependencies for downstream analysis. QIIME 2 and ANCOM-BC2 require separate bioinformatics environments.

## Limitations and Future Work

- The machine learning task distinguishes Healthy and Precancer samples; it does not directly diagnose OSCC.
- Candidate biomarkers are exploratory and require validation using independent cohorts.
- ANCOM-BC2 was performed on the full dataset, so the biomarker concordance analysis is not an independent validation.
- The predictive model's generalizability beyond the analyzed cohort remains to be established.
- Exact upstream software versions, processing parameters, and commands should be documented to improve reproducibility.

## Conclusion

This project integrates 16S rRNA sequencing analysis, microbial diversity assessment, taxonomic profiling, differential abundance testing, and machine learning to investigate oral microbiome differences between Healthy and Precancer samples.

The combined analysis identifies microbial patterns and prioritizes candidate ASVs for further investigation, demonstrating an exploratory approach to oral microbiome-based biomarker discovery.

