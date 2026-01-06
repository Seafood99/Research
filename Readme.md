# Rural Household Consumption Pattern Segmentation for Social Policy Targeting
# Segmentasi Pola Konsumsi Rumah Tangga Perdesaan untuk Targeting Kebijakan Sosial

[![Status](https://img.shields.io/badge/Status-Complete-success)]() [![Python](https://img.shields.io/badge/Python-3.8+-blue)]() [![License](https://img.shields.io/badge/License-MIT-green)]()

---

## Executive Summary / Ringkasan Eksekutif

**English:**  
This research develops a machine learning framework for rural household consumption segmentation in Indonesia using BPS aggregate data (2013-2025). We identify three distinct volatility-based clusters (Stable 77%, Volatile 18%, Extreme 5%) through K-Means clustering, achieving 98.05% classification accuracy with Random Forest. SHAP analysis reveals consumption volatility (CV) as the primary discriminator (23.26% importance). The study recommends targeting high-volatility clusters (23% coverage) for efficient social policy interventions, validated through comprehensive temporal stability and sensitivity analyses.

**Bahasa Indonesia:**  
Penelitian ini mengembangkan framework machine learning untuk segmentasi konsumsi rumah tangga perdesaan Indonesia menggunakan data agregat BPS (2013-2025). Kami mengidentifikasi tiga kluster berbasis volatilitas yang berbeda (Stabil 77%, Volatil 18%, Ekstrem 5%) melalui clustering K-Means, mencapai akurasi klasifikasi 98,05% dengan Random Forest. Analisis SHAP menunjukkan volatilitas konsumsi (CV) sebagai diskriminator utama (23,26% importance). Penelitian merekomendasikan targeting kluster volatilitas tinggi (cakupan 23%) untuk intervensi kebijakan sosial yang efisien, divalidasi melalui analisis stabilitas temporal dan sensitivitas yang komprehensif.

**Key Results / Hasil Utama:**
- **3 Clusters / 3 Kluster:** Stable/Stabil (77%), Volatile/Volatil (18%), Extreme/Ekstrem (5%)
- **Classification Accuracy / Akurasi Klasifikasi:** 98.05% (Random Forest, 5-fold CV)
- **Top Feature / Fitur Utama:** CV Consumption (23.26% SHAP importance)
- **Policy Recommendation / Rekomendasi Kebijakan:** 23% coverage targeting (High+Extreme clusters)

---

##  Full Documentation / Dokumentasi Lengkap

- **[SCIENTIFIC_FINDINGS.md](SCIENTIFIC_FINDINGS.md)** - Complete research findings and interpretations (English + Indonesian)
- **[METHODOLOGY.md](METHODOLOGY.md)** - Detailed methodological justifications (English + Indonesian)
- **[Notebooks](notebooks/)** - Step-by-step analysis (01-10)
- **[Results](results/)** - Tables and figures


---

##  Repository Structure / Struktur Repository

```
├── README.md                           ← Project overview / Ringkasan proyek
├── SCIENTIFIC_FINDINGS.md              ← Research findings / Temuan penelitian  
├── METHODOLOGY.md                      ← Methodological justifications / Justifikasi metodologi
├── data/
│   ├── dataset.csv                     ← Raw BPS data / Data mentah BPS
│   ├── cleaned_data_long.csv           ← Cleaned data / Data bersih (2,377 records)
│   ├── features_for_clustering.csv     ← Feature matrix / Matriks fitur (102 obs × 35 features)
│   ├── clustered_data.csv              ← Clustering results / Hasil clustering
│   └── vulnerability_scores.csv        ← Vulnerability metrics / Metrik kerentanan
├── notebooks/
│   ├── 01_data_cleaning.ipynb          ← Data preprocessing
│   ├── 02_feature_engineering.ipynb    ← Feature creation + CV analysis
│   ├── 03_clustering.ipynb             ← K-Means clustering (K=3)
│   ├── 04_temporal_stability.ipynb     ← Temporal analysis
│   ├── 05_cv_inequality_analysis.ipynb ← Coefficient of variation analysis
│   ├── 06_classification.ipynb         ← Random Forest + XGBoost (98.05% accuracy)
│   ├── 07_shap_analysis.ipynb          ← SHAP feature importance
│   ├── 08_policy_simulation.ipynb      ← Targeting scenarios
│   ├── 09_summary_tables.ipynb         ← Results consolidation (8 CSV + 3 LaTeX tables)
│   └── 10_final_visualizations.ipynb   ← Publication figures (4 figures, 300 DPI)
├── results/
│   ├── figures/                        ← Visualizations (PNG, 300 DPI)
│   │   ├── figure1_cluster_temporal_profiles.png
│   │   ├── figure2_feature_importance_shap.png
│   │   ├── figure3_classification_performance.png
│   │   └── figure4_policy_targeting_scenarios.png
│   ├── tables/                         ← CSV + LaTeX tables
│   │   ├── table1_clustering_metrics.csv
│   │   ├── table2_sensitivity_analysis.csv
│   │   ├── table3_cluster_profiles.csv
│   │   ├── table4_classification_comparison.csv
│   │   ├── table5_cv_detailed_results.csv
│   │   ├── table6_shap_importance.csv
│   │   ├── table7_policy_scenarios.csv
│   │   ├── table8_comprehensive_results.csv  ← Main summary table
│   │   ├── latex_cluster_profiles.tex
│   │   ├── latex_classification.tex
│   │   └── latex_shap_importance.tex
│   └── models/                         ← Saved models (RF, XGBoost)
└── scripts/                            ← Utility functions (optional)
```

---

##  Quick Start / Panduan Cepat

### Prerequisites / Prasyarat
```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost shap scipy
```

### Execution Order / Urutan Eksekusi
Run notebooks sequentially / Jalankan notebook secara berurutan:
```bash
01_data_cleaning.ipynb          # Generates cleaned_data_long.csv
02_feature_engineering.ipynb    # Generates features_for_clustering.csv
03_clustering.ipynb             # Generates clustered_data.csv (K=3 clusters)
04_temporal_stability.ipynb     # Temporal validation
05_cv_inequality_analysis.ipynb # Inequality metrics
06_classification.ipynb         # RF + XGBoost training (98.05% accuracy)
07_shap_analysis.ipynb          # Feature importance analysis
08_policy_simulation.ipynb      # Generates vulnerability_scores.csv
09_summary_tables.ipynb         # Generates 8 CSV + 3 LaTeX tables
10_final_visualizations.ipynb   # Generates 4 publication figures (300 DPI)
```

**Estimated Runtime / Perkiraan Waktu:** ~30-45 minutes for full pipeline / untuk pipeline lengkap

---

##  Key Results Summary / Ringkasan Hasil Utama

### Clustering Performance / Performa Clustering
| Metric | Value |
|--------|-------|
| Optimal K | 3 |
| Silhouette Score | 0.3972 |
| Davies-Bouldin Index | 0.9527 |
| Calinski-Harabasz Score | 64.45 |

### Cluster Characteristics / Karakteristik Kluster
| Cluster | Size | % | CV Consumption | Food Ratio | Interpretation |
|---------|------|---|----------------|------------|----------------|
| 0: Stable | 79 | 77% | 119.77 | 53.05% | Low volatility, balanced structure |
| 1: Volatile | 18 | 18% | 302.82 | 13.78% | High volatility, non-food dominant |
| 2: Extreme | 5 | 5% | 242.57 | 38.91% | Very high volatility, highest consumption |

### Classification Performance / Performa Klasifikasi
| Model | Mean Accuracy | Std Dev | Selected |
|-------|---------------|---------|----------|
| **Random Forest** | **98.05%** | **±2.67%** | ✅ |
| XGBoost | 96.10% | ±3.45% | - |

**Paired t-test:** p=0.3739 (no significant difference, RF selected for stability + interpretability)

### Top 10 SHAP Features / 10 Fitur SHAP Teratas
1. CV Consumption: 23.26%
2. Food Ratio: 15.71%
3. Non-Food Ratio: 15.17%
4. CV Food: 10.08%
5. CV Non-Food: 8.90%
6. Clothing Ratio: 7.16%
7. Housing Ratio: 6.00%
8. Education Ratio: 5.09%
9. Durable Goods Ratio: 4.63%
10. Health Ratio: 4.00%

**Top 3 features account for 54.14% of model decisions**

### Policy Targeting Scenarios / Skenario Targeting Kebijakan
| Scenario | Coverage | Avg Vuln Score | Efficiency | Recommendation |
|----------|----------|----------------|------------|----------------|
| A: Extreme Only | 5% | 74.1 | 14.82 | Too narrow |
| **B: High+Extreme** | **23%** | **62.3** | **2.71** | ✅ **RECOMMENDED** |
| C: Universal | 100% | 34.8 | 0.35 | Inefficient |
| D: Top 30% Score | 30% | 58.6 | 1.95 | Alternative |

---

## Methodology Overview / Ringkasan Metodologi

### Data Source / Sumber Data
- **Source:** BPS (Badan Pusat Statistik) Indonesia
- **Period:** 2013-2025 (13 years)
- **Unit:** Aggregate regional-level consumption data
- **Features:** 35 engineered features (ratios + CV metrics)

### Analysis Pipeline / Pipeline Analisis
1. **Data Cleaning:** Handle BPS multi-header format, normalize numeric values
2. **Feature Engineering:** Calculate CV metrics, consumption ratios (food/non-food/categories)
3. **Clustering:** K-Means (K=3) validated by Hierarchical + GMM
4. **Temporal Analysis:** Cluster persistence validation (transition rate <15%)
5. **Classification:** Random Forest (98.05% accuracy, 5-fold stratified CV)
6. **Interpretability:** SHAP analysis for feature importance
7. **Policy Simulation:** 4 targeting scenarios with vulnerability scoring
8. **Validation:** Sensitivity analysis (K=2-5), paired t-test (RF vs XGBoost)

### Key Methodological Decisions / Keputusan Metodologi Utama
- **Why K=3?** Optimal trade-off between clustering quality (Silhouette=0.3972) and interpretability (meaningful stable/volatile/extreme segments)
- **Why Random Forest?** Higher accuracy (98.05% vs 96.10%), lower variance (±2.67% vs ±3.45%), better interpretability
- **Why CV metrics?** Scale-invariant measure of consumption volatility, enables cross-regional comparison
- **Why 23% coverage?** Scenario B captures all high-volatility clusters (1+2) with efficiency ratio 2.71

See [METHODOLOGY.md](METHODOLOGY.md) for detailed justifications.

---

## How to Use Results / Cara Menggunakan Hasil

### For Paper Writing / Untuk Penulisan Paper
- **Introduction:** Use Executive Summary above
- **Methods:** Copy relevant sections from [METHODOLOGY.md](METHODOLOGY.md)
- **Results:** Copy from [SCIENTIFIC_FINDINGS.md](SCIENTIFIC_FINDINGS.md) Sections 2-5
- **Discussion:** Copy from [SCIENTIFIC_FINDINGS.md](SCIENTIFIC_FINDINGS.md) Sections 6-8
- **Tables:** Insert LaTeX tables from `results/tables/latex_*.tex`
- **Figures:** Insert PNG figures from `results/figures/figure*.png`

### For LaTeX Integration / Untuk Integrasi LaTeX
```latex
% Main summary table
\input{tables/table8_comprehensive_results.csv}

% Cluster profiles
\input{tables/latex_cluster_profiles.tex}

% Classification comparison
\input{tables/latex_classification.tex}

% SHAP importance
\input{tables/latex_shap_importance.tex}

% Figures
\begin{figure}[h]
    \centering
    \includegraphics[width=0.9\textwidth]{figures/figure1_cluster_temporal_profiles.png}
    \caption{Cluster consumption volatility and food ratio trends (2013-2025)}
    \label{fig:temporal_profiles}
\end{figure}
```

---

##  Research Contributions / Kontribusi Penelitian

1. **Methodological:** Multi-method clustering validation (K-Means + Hierarchical + GMM) with comprehensive sensitivity analysis
2. **Analytical:** SHAP-based interpretability demonstrates CV consumption as primary discriminator (23.26%)
3. **Policy-Relevant:** Data-driven targeting framework with 4 evaluated scenarios, recommending 23% coverage
4. **Temporal Validation:** Cluster stability analysis confirms structural persistence (transition rate <15%)
5. **High Accuracy:** 98.05% classification accuracy enables confident algorithmic targeting

---
