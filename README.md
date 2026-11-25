# Loan Default Warning System

**Machine Learning Lab (AIP301) – Project-Based Learning, Batch A2**
Indian Institute of Information Technology Kota, Department of Computer Science Engineering

**Topic:** Building a Reliable Loan Default Warning System Under Missing Data and Changing Conditions

**Submitted to:** Dr. Shibna Hussain

| Name | Roll No. |
|---|---|
| Apoorv Mishra | 2024KUCP1035 |
| Nishant Yadav | 2024KUCP1036 |
| Vinay Soni | 2024KUCP1046 |
| Saksham Agarwal | 2024KUCP1054 |

---

## What this project does

A lender wants to know which loan applicants may not repay. This project builds a decision-support system that gives each applicant a risk score, and tests honestly how well it holds up when data is missing, noisy, or from a different time period.

We care more about honest evaluation than a high score on one lucky split. The report explains what worked, what failed, and how we recovered.

## Dataset

[Home Credit Default Risk (Kaggle)](https://www.kaggle.com/competitions/home-credit-default-risk)

- File used: `application_train.csv` (307,511 applicants, 122 columns)
- Target column: `TARGET` (1 = defaulted, 0 = repaid). About 8% defaulted.
- Only the main table was used. The other Home Credit tables were not joined.

**The dataset is not included in this repo** because the files are very large. Download `application_train.csv` from the Kaggle link above.

## Methods used (only from our lab course)

Logistic Regression (L1 and L2), KNN, Decision Tree, SVM (RBF), Random Forest and K-Means, all from scikit-learn. We also used numpy, pandas and matplotlib.

## Project flow (notebook parts)

| Part | What it covers |
|---|---|
| 1 | Data audit, 85% / 15% split (final test locked away), two leakage demos |
| 2 | Missing values: 3 methods, hiding extra 10% / 25% / 40% of values, 11 new features |
| 3 | Applicant "stories" (default rate by income, age, education and so on) and K-Means customer groups |
| 4 | Models, train vs validation curves, learning curves, cost table and threshold choice |
| 5 | Three split designs, 5 random seeds, feature removal, feature noise, weak segments, model combining |
| 6 | Time and memory, CPU cores, calibration, uncertain cases, final test (run once), report outline |

## How to run

1. Open `ML_Lab_Proj_A2_.ipynb` in Google Colab.
2. Mount Google Drive and put `application_train.csv` in the project folder. Change the folder path in the first cell if yours is different.
3. Run Parts 1 to 6 in order. Each part loads the files saved by the earlier part.
4. A fixed seed (24) is used, except where the experiment changes the seed on purpose.

Files the notebook creates while running:

| File | Created in | Contents |
|---|---|---|
| `dev.csv` | Part 1 | 85% of the data, used for everything |
| `final_test.csv` | Part 1 | 15% of the data, not opened until the final step |
| `dev_fe.csv` | Part 2 | dev plus new features |
| `dev_fe2.csv` | Part 3 | dev plus new features plus K-Means group number |
| `experiment_log.csv` | All parts | One row per run: data version, split, features, settings, run time, results, notes |

## Main results

| Measure | Result |
|---|---|
| Final test ROC-AUC | 0.743 |
| Recall (real defaulters caught) | 24.9% |
| Precision (flagged people who really defaulted) | 27.9% |
| Total cost (false approval = 5 × false rejection) | 16,383 |
| Cost if everyone is approved | about 18,600 (we save about 12%) |

Other findings:

- The three EXT_SOURCE credit scores carry most of the signal. Removing them drops ROC-AUC from 0.7525 to 0.6888.
- The time-based split (0.737) is lower than the random split (0.7525), so random splits flatter the model.
- Over 5 random seeds, ROC-AUC is 0.7464 ± 0.0043.
- Raw Random Forest risk scores were far too high. Isotonic regression fixed this and kept ranking quality.
- Our new features and K-Means group number only helped slightly (+0.0023 and +0.0002 ROC-AUC). They are more useful for explaining applicants than for predicting.

## What the system should and should not be used for

**Should:** rank applicants by risk, send borderline cases to a human loan officer, and act as a cost-aware helper.

**Should not:** make fully automatic rejections, be used on a very different group of applicants without retraining, or be used without regular re-checking of its risk scores.

## Known limitations

- Only the main table was used. Bureau and installment history were not added.
- There is no person ID, so repeat applicants cannot be detected.
- `DAYS_ID_PUBLISH` is only a rough stand-in for time.
- The cost values are our assumption, not the lender's real numbers.
- One K-Means group contains a single outlier applicant. The fix (capping income at the 99th percentile) was not rerun.
- The two leakage demos showed almost no difference because the dataset is large.
- SVM and KNN were trained on a 15,000-row sample with weak settings.

## Report

The full typed report is in `ML_Lab_Loan_Default_Report_Batch_A2.pdf`. The hand-written reflection is submitted separately.

## Repo contents

```
ML_Lab_Proj_A2_.ipynb                  main notebook (Parts 1 to 6)
experiment_log.csv                     log of every run
ML_Lab_Loan_Default_Report_Batch_A2.pdf  typed report
README.md                              this file
```

CSV data files are not uploaded because of their size.
