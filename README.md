# AMR Prediction Under Different Generalization Settings

## Overview

A machine-learning model can perform well on a test set without necessarily learning patterns that transfer to new biological entities.

In antimicrobial resistance (AMR) prediction, multiple records can share the same species, antibiotic, species–antibiotic combination, or genome. A random train-test split separates rows, but it does not necessarily separate these underlying relationships.

This project examines how model performance changes when different biological relationships are kept out of training. Rather than searching for the best classifier, the focus is on understanding what each evaluation strategy actually measures.

## Dataset

The dataset was compiled from genome-level AMR records available through the Bacterial and Viral Bioinformatics Resource Center (BV-BRC).

| Characteristic | Count |
|---|---:|
| Total records | 1,493 |
| Labelled observations | 801 |
| Species | 5 |
| Genomes | 41 |
| Antibiotics | 46 |
| Species–antibiotic combinations | 82 |

The target variable is the recorded Resistant Phenotype (Resistant or Susceptible). Unlabelled observations were excluded from model evaluation.

These are BV-BRC-recorded phenotype labels, not independently verified clinical ground truth.

## Evaluation Strategy

Four evaluation regimes were used to examine different forms of generalization.

| Regime | What is held out |
|---|---|
| Random row-level | Individual rows |
| Unseen Species–Antibiotic | Species–antibiotic combinations |
| Unseen Genome | Individual genomes |
| Leave-One-Species-Out (LOSO) | Entire species |

The grouped evaluations use five-fold GroupKFold, while the random evaluation uses stratified five-fold cross-validation. LOSO holds out one species at a time.

These regimes test different types of novelty. Holding out a genome does not necessarily mean that its species or species–antibiotic relationship is also unseen.

## Models

Four classifiers were evaluated:

- Logistic Regression
- Random Forest
- Gradient Boosting
- XGBoost

The models were evaluated using accuracy, balanced accuracy, F1-score, ROC-AUC, PR-AUC, sensitivity, and specificity.

The results below report Gradient Boosting, which is used for the main comparison across evaluation regimes.

## Results

| Evaluation regime | Accuracy | F1-score |
|---|---:|---:|
| Random row-level | 0.824 | 0.820 |
| Unseen Species–Antibiotic | 0.628 | 0.595 |
| Unseen Genome | 0.824 | 0.813 |
| Leave-One-Species-Out | 0.438 | 0.492 |

The results show that performance depends on the type of observation held out.

- Accuracy decreases by approximately 19.6 percentage points when species–antibiotic combinations are held out instead of individual rows.
- Unseen-genome accuracy remains at 0.824. However, the split-integrity audit shows that species and species–antibiotic relationships still overlap between training and test sets.
- LOSO accuracy is 0.438, indicating substantially weaker performance when the entire species is held out.

### Majority Baselines

Simple majority-based models were also evaluated to understand how much of the observed performance could be explained by recurring relationships.

| Evaluation regime | Baseline | Accuracy |
|---|---|---:|
| Random row-level | Overall Majority | 0.499 |
| Random row-level | Species + Antibiotic Majority | 0.838 |
| Unseen Species–Antibiotic | Overall Majority | 0.415 |
| Unseen Species–Antibiotic | Species Majority | 0.604 |

An important observation is that the Species + Antibiotic majority baseline scores 0.838 under random splitting, slightly higher than Gradient Boosting at 0.824. This suggests that recurring species–antibiotic relationships account for a substantial part of the apparent performance in this evaluation setting.

## Split-Integrity Audit

The project measures how often biological relationships in the test set also occur in the training set.

| Evaluation regime | Species overlap | Genome overlap | Species–Antibiotic overlap |
|---|---:|---:|---:|
| Random row-level | 100% | 100% | 100% |
| Unseen Species–Antibiotic | 100% | 100% | 0% |
| Unseen Genome | 100% | 0% | 100% |
| LOSO | 0% | 0% | 0% |

This helps explain why the unseen-genome result remains similar to the random-split result: the model has not encountered the test genomes, but it has encountered the corresponding species and species–antibiotic relationships.

## Additional Analyses

The notebook also includes:

- Feature ablation experiments.
- Computational Method feature ablation.
- Model-seed robustness testing across 10 seeds.
- Label-permutation testing with 200 permutations.
- Bootstrap confidence intervals using 1,000 resamples.
- SHAP and permutation-based feature importance.
- Label-conflict analysis for Species–Antibiotic and Genome–Antibiotic relationships.
- Error analysis by species and antibiotic class.

For the unseen-genome permutation test, the observed accuracy was 0.824, compared with a null mean of 0.499 (standard deviation 0.023; p = 0.005).

## Repository Structure

| File | Description |
|---|---|
| `BVBRC_genome_amr.csv` | Dataset used for analysis |
| `amr_leakage_analysis.ipynb.ipynb` | Main analysis notebook |
| `regime_comparison.png` | Performance comparison across evaluation regimes |
| `dependency_structure.png` | Relationship and dependency structure |
| `Feature Ablation.png` | Feature ablation results |
| `feature_importance.png` | Feature importance analysis |
| `shap_summary.png` | SHAP summary |
| `permutation_test.png` | Permutation test results |
| `seed_robustness.png` | Model-seed robustness |
| `conflict_analysis.png` | Label-conflict analysis |
| `diagnostics.png` | Model diagnostics |
| `learning_curve.png` | Learning curve analysis |

## Running the Project

The analysis is implemented in a Jupyter notebook and can be run using Google Colab or a local Jupyter environment.

1. Clone or download this repository.
2. Open `amr_leakage_analysis.ipynb.ipynb`.
3. Make sure `BVBRC_genome_amr.csv` is available in the notebook's working directory.
4. If using Google Colab, upload the CSV and update the data path in the notebook if necessary.
5. Install the required libraries if they are not already available:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn xgboost shap
```

6. Run the notebook from top to bottom.

The notebook uses a fixed master seed of 42. Additional seeds and resampling settings are defined in the notebook.

## Limitations

- The dataset contains only 801 labelled observations across five species.
- The phenotype labels are recorded BV-BRC values, not independently verified laboratory ground truth.
- The results depend on the dataset composition and evaluation strategy.
- LOSO is exploratory because only five species are available.
- No independent external dataset was used to validate generalization.
- Predictive accuracy does not establish clinical usefulness or readiness for deployment.

## Conclusion

The central finding is that model performance depends on what is considered an unseen observation.

A random row-level split can produce a different estimate of performance from evaluations that hold out species–antibiotic relationships or entire species. The split-integrity audit and majority baselines show why these distinctions matter.

For AMR prediction, reporting an accuracy score without explaining the evaluation split can give an incomplete picture of how well a model generalizes.
