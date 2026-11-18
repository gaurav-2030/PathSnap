# PathSnap — GPS Map-Matching

Match noisy GPS points to the road segment the vehicle was actually on.

**Approach:** for every GPS point we collect nearby road segments (candidates), describe each (point, candidate) pair with features, and train a classifier to score which candidate is correct. Seven models are compared.

## Folders

| Folder | What is inside |
|---|---|
| `pathsnap/` | Reusable code: reading the raw files (`data_io.py`), geometry, candidate generation, label alignment and features (`core.py`), metrics (`evaluation.py`), Folium maps (`maps.py`) |
| `scripts/` | Steps to run, in order |
| `results/` | Tables (CSV), figures, Folium maps and run logs |
| `artifacts/models/` | Trained models |
| `data/raw/` | Put the dataset here (not committed) |

## Steps

| Script | What it does | Output |
|---|---|---|
| `01_explore_dataset.py` | Dataset exploration: counts, missing values, time gaps, network size and connectivity, plots, Folium maps | `results/eda_*.csv`, `results/figures/eda_*`, `results/maps/` |
| `02_build_dataset.py` | Converts coordinates to metres, generates candidate road segments, assigns a reference segment to every GPS point, computes 22 features | `data/processed/`, `results/candidate_stats.csv` |
| `03_train_models.py` | Trains 7 models with 5-fold cross-validation grouped by trip, evaluates on the student-test trips | `results/model_comparison.csv`, `artifacts/models/` |
| `07_plots.py` | Comparison plots | `results/figures/` |

## How to run

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Place the data as `data/raw/train/<trip_id>/` (80 trips) and `data/raw/student_test/<trip_id>/` (10 trips), then:

```bash
python scripts/01_explore_dataset.py
python scripts/02_build_dataset.py
python scripts/03_train_models.py
python scripts/07_plots.py
```

## Results so far (student-test, 10 trips)

| Model | CV Macro F1 | Top-1 accuracy |
|---|---|---|
| Logistic Regression | 0.937 | 0.883 |
| Gaussian Naive Bayes | 0.925 | 0.858 |
| KNN | 0.931 | 0.876 |
| Decision Tree | 0.935 | 0.890 |
| Random Forest | 0.940 | 0.897 |
| HistGradientBoosting | 0.939 | 0.890 |
| MLP | 0.938 | 0.896 |

Top-1 accuracy = share of GPS points whose highest-scored candidate is the correct road segment.
