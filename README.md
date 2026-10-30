# Apparent Performance of Antimicrobial Resistance Phenotype Prediction Depends on the Definition of an Unseen Observation

Most machine-learning studies of antimicrobial resistance (AMR) evaluate their models by splitting rows at random into training and test sets. This separates individual observations, but does not necessarily prevent the same species, antibiotic, species–antibiotic pairing, or genome from appearing in both sets.

This project investigates how the definition of unseen data affects the measured performance of machine-learning models for antimicrobial resistance phenotype prediction.

## Overview

The analysis uses a genome-level AMR dataset compiled from [BV-BRC](https://www.bv-brc.org/), containing:

- 801 labelled observations
- 5 bacterial species
- 41 genomes
- 46 antibiotics
- 82 species–antibiotic combinations

Four classifiers were evaluated:

- Logistic Regression
- Random Forest
- Gradient Boosting
- XGBoost

## Evaluation Regimes

Four evaluation strategies were used to examine how model performance changes depending on what is held out during testing.

| Evaluation regime | What's held out |
|---|---|
| Random row-level split | Individual observations |
| Unseen Species–Antibiotic | Species–antibiotic combinations |
| Unseen Genome | Individual genomes |
| Leave-One-Species-Out | Entire species |

## Key Findings

The results demonstrate that measured prediction accuracy depends on the evaluation strategy and the biological relationships represented in the training and test sets.

| Evaluation regime | Accuracy |
|---|---:|
| Random row-level split | 0.824 |
| Unseen species–antibiotic combinations | 0.628 |
| Unseen genomes | 0.824 |
| Leave-One-Species-Out | 0.406 |

The results show:

- Random row-level evaluation produced an accuracy of **0.824**.
- Holding out species–antibiotic combinations reduced accuracy to **0.628**, a decrease of 19.6 percentage points.
- Holding out individual genomes resulted in an accuracy of **0.824**, showing that genome-level separation alone does not necessarily eliminate overlap in species–antibiotic relationships.
- Leave-One-Species-Out evaluation produced a reported pooled out-of-fold accuracy of **0.406**.

## What's in This Repository

- Data preprocessing and feature construction using Species, Antibiotic, Antibiotic_Class, and Computational Method.
- Four evaluation regimes implemented using stratified k-fold cross-validation and GroupKFold.
- Split-integrity auditing to measure overlap between training and test sets across different relationship types.
- Relationship-based majority-vote baselines using Species + Antibiotic and Genome + Antibiotic.
- Feature ablation studies.
- Model-seed robustness testing across 10 seeds.
- Label-permutation significance testing using 200 permutations per evaluation regime.
- Label-conflict analysis.

## Methods Summary

### Models

- Logistic Regression (C = 1.0)
- Random Forest (300 trees)
- Gradient Boosting (100 estimators, maximum depth 3)
- XGBoost

### Validation

- Five-fold stratified cross-validation and GroupKFold were used depending on the evaluation regime.
- Pooled out-of-fold accuracy was used as the primary metric for Leave-One-Species-Out evaluation because the held-out folds were unbalanced.

### Statistical Analysis

- Bootstrap confidence intervals using 1,000 resamples.
- Label-permutation significance testing using 200 permutations per evaluation regime.
- SHAP-based feature importance.
- Permutation feature importance.
- Model-seed robustness testing across 10 seeds.
- Feature ablation and label-conflict analysis.
- Split-integrity checks and relationship-based majority-vote baselines.

## Tools and Technologies

- Python
- Pandas
- NumPy
- Scikit-learn
- XGBoost
- SHAP

## Data Availability

The analytical dataset was compiled from publicly available, de-identified genome-level AMR records hosted by [BV-BRC](https://www.bv-brc.org/).

No patient-identifiable clinical data were used, and no new human or animal data were collected.

## Limitations

- The dataset contains 801 labelled observations across five bacterial species, limiting the breadth of conclusions that can be drawn.
- Results depend on dataset composition and the evaluation strategy used.
- Performance on the evaluated species does not establish generalization to all bacterial species, antibiotics, or clinical settings.
- The recorded AMR phenotypes are derived from the available dataset and are not independent clinical validation.
- Predictive accuracy alone does not establish clinical usefulness or readiness for deployment.

## Conclusion

Evaluating AMR prediction models requires more than reporting a single accuracy score.

The definition of an unseen observation determines what kind of generalization is being tested. Random row-level splits, unseen species–antibiotic combinations, unseen genomes, and entirely unseen species represent different evaluation challenges.

Clearly defining the held-out biological entity and verifying train/test separation are essential for interpreting machine-learning performance in antimicrobial resistance prediction.
