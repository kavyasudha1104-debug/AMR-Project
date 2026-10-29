# Apparent Performance of Antimicrobial Resistance Phenotype Prediction Depends on the Definition of an Unseen Observation

Most machine-learning studies of antimicrobial resistance (AMR) evaluate their models by splitting rows at random into training and test sets. That keeps individual rows apart, but it does nothing to stop the same species, antibiotic, species–antibiotic pairing, or genome from turning up on both sides of the split.

This project measures how much that choice actually matters.

## Overview

Using a genome-level AMR dataset from [BV-BRC](https://www.bv-brc.org/) containing 801 labelled observations spanning 5 species, 41 genomes, 46 antibiotics, and 82 species–antibiotic combinations, four classifiers were evaluated under four different definitions of unseen data.

The models used were **logistic regression, random forest, gradient boosting, and XGBoost**.

| Evaluation regime | What's held out |
|---|---|
| Random row-level split | Individual rows |
| Unseen Species–Antibiotic | Species–antibiotic combinations |
| Unseen Genome | Individual genomes |
| Leave-One-Species-Out | Entire species |

## Key Findings

Accuracy is not a fixed property of a model. It can change substantially depending on what is held out during evaluation.

- **0.824 accuracy** under random row-level splitting.
- **0.628 accuracy** when species–antibiotic combinations are held out, a decrease of 19.6 percentage points.
- **0.824 accuracy** when individual genomes are held out, showing that holding out genomes does not necessarily eliminate overlap in species–antibiotic relationships.
- **0.406 pooled out-of-fold accuracy** under leave-one-species-out evaluation, reported as indistinguishable from a permutation-shuffled null (P = 1.000).

These findings highlight the importance of defining which biological entity is meant to be unseen and verifying that the train/test split actually enforces that separation. A random split and an evaluation involving genuinely unseen biological entities do not measure the same kind of generalization.

## What's in This Repository

- Data preprocessing and feature construction using Species, Antibiotic, Antibiotic_Class, and Computational Method.
- Four evaluation regimes implemented using stratified k-fold cross-validation and GroupKFold.
- Split-integrity auditing to measure train/test overlap across different relationship types.
- Relationship-based majority-vote baselines using Species + Antibiotic and Genome + Antibiotic.
- Feature ablation studies.
- Model-seed robustness testing across 10 seeds.
- Label-permutation significance testing using 200 permutations per evaluation regime.
- Label-conflict analysis.

## Methods Summary

### Models

Four classifiers were evaluated:

- Logistic Regression (C = 1.0)
- Random Forest (300 trees)
- Gradient Boosting (100 estimators, maximum depth 3)
- XGBoost

### Validation

Five-fold stratified cross-validation and GroupKFold were used depending on the evaluation regime.

Pooled out-of-fold accuracy was reported as the primary metric for the leave-one-species-out setting because the held-out folds were unbalanced.

### Statistical Analysis

- Bootstrap confidence intervals using 1,000 resamples.
- Label-permutation significance tests using 200 permutations per evaluation regime.
- SHAP-based feature importance.
- Permutation feature importance.
- Model-seed robustness testing across 10 seeds.
- Feature ablation and label-conflict analyses.
- Split-integrity checks and relationship-based majority-vote baselines.

## Tools and Technologies

- Python
- Scikit-learn
- XGBoost
- SHAP

## Data Availability

The analytical dataset was compiled from publicly available, de-identified genome-level AMR records hosted by [BV-BRC](https://www.bv-brc.org/).

No patient-identifiable clinical data were used, and no new human or animal data were collected.

## Limitations

- The dataset contains 801 labelled observations across five species, limiting the breadth of conclusions that can be drawn.
- Results depend on the dataset composition and the evaluation strategy used.
- Performance on the evaluated species does not establish generalization to all bacterial species, antibiotics, or clinical settings.
- Predictive accuracy alone does not establish clinical usefulness or readiness for deployment.

## Conclusion

Evaluating AMR prediction models requires more than reporting a single accuracy score. The definition of an unseen observation determines what kind of generalization is being tested.

Random row-level splits can produce a different picture of model performance from splits that hold out species–antibiotic relationships or entire species. Explicitly defining the held-out biological entity and auditing train/test separation are essential for interpreting AMR prediction results.
