# Diabetes Prediction - Data Mining Project

A complete data mining project on the Pima Indians Diabetes dataset, covering classification, clustering, and association rule mining. Includes from-scratch implementations of STING and CLIQUE, and automatic parameter tuning for DBSCAN.

## About the Dataset

- Source: Pima Indians Diabetes Dataset
- 768 patients (all female, Pima Indian heritage, age 21+)
- 8 medical features + binary Outcome (0 = non-diabetic, 1 = diabetic)
- Class distribution: ~65% non-diabetic, ~35% diabetic

| Column | Description |
|--------|-------------|
| Pregnancies | Number of pregnancies |
| Glucose | Blood glucose level |
| BloodPressure | Diastolic blood pressure (mm Hg) |
| SkinThickness | Triceps skin fold thickness (mm) |
| Insulin | Serum insulin level |
| BMI | Body Mass Index |
| DiabetesPedigreeFunction | Genetic diabetes likelihood function |
| Age | Age in years |
| Outcome | 0 = non-diabetic, 1 = diabetic |

## Objectives

1. Predict diabetes using several classification algorithms and compare them.
2. Discover natural groupings of patients using clustering algorithms.
3. Extract association rules that describe relationships between features and the disease.

## Methodology

### Data Preprocessing
The columns Glucose, BloodPressure, SkinThickness, Insulin, and BMI contain 0 values that are physiologically impossible. These are replaced with NaN and imputed with the column median. The data is then split 80/20 with stratification, and scaled with StandardScaler.

### Outlier Detection
Z-scores are computed for Glucose; any point with |z| > 3 is flagged as an outlier.

### Classification Models

| Model | Parameters |
|-------|-----------|
| K-Nearest Neighbors | k=5, on scaled data |
| Decision Tree | max_depth=5 |
| Gaussian Naive Bayes | Default |
| Random Forest | 100 trees |
| XGBoost | 100 trees, max_depth=5, learning_rate=0.1 |
| KNN (tuned) | GridSearchCV over k, weights, metric |
| Random Forest (tuned) | GridSearchCV over n_estimators, depth, split/leaf sizes |

Evaluation: Accuracy, Precision/Recall, F1-Score, 5-Fold Cross-Validation, Confusion Matrix.

### Clustering Algorithms

| Algorithm | Implementation |
|-----------|---------------|
| K-Means | scikit-learn |
| K-Medoids | scikit-learn-extra |
| DBSCAN | scikit-learn with automatic (eps, min_samples) grid search |
| STING | Built from scratch: quadtree hierarchy + BFS merging of adjacent dense cells |
| CLIQUE | Built from scratch: 1D dense units, 2D candidate generation, subspace selection |

### Association Rules
Apriori with min_support=0.1 and min_confidence=0.6 on discretized versions of Glucose, BMI, Age, and Outcome.

## Results

### Classification

| Model | Test Accuracy | Test F1 | 5-Fold CV F1 |
|-------|:-:|:-:|:-:|
| Random Forest (tuned) | 0.760 | 0.641 | 0.646 |
| KNN (tuned) | 0.734 | 0.586 | 0.645 |
| Random Forest | 0.779 | 0.653 | 0.643 |
| Naive Bayes | 0.701 | 0.596 | 0.627 |
| KNN (k=5) | 0.753 | 0.635 | 0.610 |
| XGBoost | 0.747 | 0.629 | 0.602 |
| Decision Tree | 0.760 | 0.678 | 0.564 |

Tuning KNN with GridSearchCV added +3.5 points to its CV F1, a larger gain than switching to XGBoost.

### Clustering

| Algorithm | Silhouette | Notes |
|-----------|:-:|-------|
| STING | 0.446 | 2 clusters (42 dense cells) |
| DBSCAN (eps=1.40) | 0.260 | 2 clusters, 221 noise points |
| CLIQUE | 0.241 | Best subspace: Pregnancies x Glucose |
| K-Means | 0.193 | 2 clusters |
| K-Medoids | 0.178 | 2 clusters |

The hierarchical STING grid produced the strongest 2D structure. CLIQUE automatically chose Pregnancies x Glucose as the densest subspace.

### Association Rules

| Rule | Confidence | Lift |
|------|:-:|:-:|
| Age_Middle + Glucose_High -> Diabetes | 0.77 | 2.21 |
| BMI_Obese + Glucose_High -> Diabetes | 0.76 | 2.17 |
| Glucose_High -> Diabetes | 0.69 | 1.97 |
| Age_Middle + BMI_Obese -> Diabetes | 0.61 | 1.75 |
| Glucose_Low -> Age_Young + No_Diabetes | 0.65 | 1.53 |

High glucose combined with obesity or middle age more than doubles the baseline diabetes rate.

### Feature Importance (Random Forest)

1. Glucose (27.4%)
2. BMI (16.2%)
3. DiabetesPedigreeFunction (12.5%)
4. Age (11.3%)
5. Insulin, BloodPressure, Pregnancies, SkinThickness (smaller contributions)

## How to Run

```bash
git clone https://github.com/<username>/Diabetes_Prediction.git
cd Diabetes_Prediction
pip install -r requirements.txt
jupyter notebook Diabetes_Prediction_DATA_MINING.ipynb
```

## Project Structure

```
Diabetes_Prediction/
├── Diabetes_Prediction_DATA_MINING.ipynb
├── diabetes.csv
├── requirements.txt
├── .gitignore
└── README.md
```

## Notes

- The STING and CLIQUE implementations are complete (not simplified grid binning).
- DBSCAN's (eps, min_samples) are selected by silhouette score, not hand-picked.
- The data is specific to Pima Indian women aged 21+, so results may not generalize to other populations.

## Author

Shahad Hadadi - College of Engineering and Computer Science, Jazan University
