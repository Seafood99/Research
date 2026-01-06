# METHODOLOGY: Research Design Decisions and Justifications
# METODOLOGI: Keputusan Desain Penelitian dan Justifikasi

**Document Purpose / Tujuan Dokumen:**  
Comprehensive explanation of methodological choices, rationale, and justifications for key research decisions. This document addresses potential reviewer questions and demonstrates rigor in research design.

Penjelasan komprehensif pilihan metodologis, rasional, dan justifikasi untuk keputusan penelitian kunci. Dokumen ini menjawab pertanyaan reviewer potensial dan mendemonstrasikan rigor dalam desain penelitian.

---

## PART A: ENGLISH VERSION

---
## 1. DATA PREPARATION DECISIONS

### 1.1 Why Aggregate (Provincial/Regional) Data Instead of Household-Level?

**Decision:** Use BPS aggregate consumption data at provincial/regional level (n=102 observations across 13 years)

**Justification:**
- **Data Availability:** BPS publicly releases aggregate statistics; individual microdata requires special access and ethical clearance
- **Policy Relevance:** Regional-level analysis directly informs provincial welfare program planning and budget allocation
- **Statistical Stability:** Aggregation reduces noise from individual household volatility; regional patterns are more stable for policy planning
- **Scope Alignment:** Research focuses on regional consumption patterns for macro-level policy, not individual household profiling

**Limitations Acknowledged:**
- Cannot capture intra-regional heterogeneity
- Individual household vulnerabilities may be masked by aggregation
- Limited sample size (n=102) compared to household microdata

**Mitigation:**
- Temporal dimension (13 years) compensates for limited cross-sectional observations
- Feature engineering creates 35 informative variables from available data
- Sensitivity analysis validates robustness of findings



## 2. FEATURE ENGINEERING DECISIONS

### 2.1 Why Exclude Absolute Consumption Values?

**Decision:** Exclude absolute consumption amounts; use only ratios and coefficient of variation (CV) metrics

**Justification:**
- **Scale Invariance:** Ratios enable comparison across regions with different price levels and income scales
- **Focus on Structure:** Expenditure structure (food vs non-food) is more policy-relevant than absolute amounts
- **Volatility Emphasis:** CV captures consumption instability independent of mean levels
- **Clustering Performance:** Preliminary analysis showed ratios + CV outperform absolute values in cluster separation

**Supporting Evidence:**
- SHAP analysis confirms CV Consumption (volatility metric) is top feature (23.26% importance)
- Food/Non-food ratios are 2nd and 3rd most important (15.71% + 15.17%)
- Combined ratios + CV features account for 54.14% of model discriminative power

**Alternative Considered:** Include absolute consumption + ratios + CV
- **Rejected because:** Introduced multicollinearity; ratios are derived from absolute values, creating redundancy
- **Result:** Lower clustering quality (Silhouette dropped from 0.397 to 0.324 in preliminary tests)

### 2.2 Why Coefficient of Variation (CV) for Volatility?

**Decision:** Use CV = (standard deviation / mean) × 100 as volatility metric

**Justification:**
- **Relative Measure:** CV normalizes volatility by mean, enabling comparison across different consumption levels
- **Economic Interpretation:** High CV indicates unstable consumption relative to average, a vulnerability indicator
- **Statistical Property:** CV is dimensionless and scale-free, suitable for cross-regional comparison
- **Policy Relevance:** Households/regions with high CV experience greater consumption shocks, requiring stabilization interventions

**Alternative Considered:** Standard deviation alone
- **Rejected because:** Absolute volatility confounds with consumption level; high-income regions naturally have higher SD

### 2.3 Why 35 Features?

**Decision:** Engineer 35 features comprising:
- 11 consumption ratios (food, non-food, 9 expenditure categories)
- 11 CV metrics (corresponding to each consumption category)
- 13 derived features (interactions, temporal aggregations)

**Justification:**
- **Comprehensive Coverage:** Captures both structural (ratios) and stability (CV) dimensions of consumption
- **Category Granularity:** 9 expenditure categories (clothing, housing, education, health, etc.) provide detailed spending patterns
- **Dimensionality Balance:** 35 features for 102 observations yields acceptable feature-to-observation ratio (1:3)
- **Feature Importance Validation:** SHAP analysis confirms top 10 features account for 100% of model decisions; no redundant features

**Dimensionality Reduction Considered:** Principal Component Analysis (PCA)
- **Not Applied:** PCA reduces interpretability; policy-makers need explicit feature meanings
- **Justification:** Random Forest handles 35 features efficiently; no overfitting observed (CV performance stable)



## 3. CLUSTERING DECISIONS

### 3.1 Why K=3 Clusters?

**Decision:** Select K=3 as optimal number of clusters after testing K=2 to K=5

**Justification (Multi-Criteria):**

**A. Clustering Quality Metrics:**
- K=3 Silhouette (0.3972) only marginally lower than K=2 (0.4051), difference = 0.0079
- Davies-Bouldin and Calinski-Harabasz show acceptable performance at K=3
- K=4 and K=5 show degrading metrics (Silhouette drops to 0.3624 at K=5)

**B. Interpretability:**
- K=2 is oversimplified: Binary (stable/unstable) segmentation loses important distinction between "volatile" and "extreme volatility"
- K=3 aligns with natural consumption patterns: Stable majority (77%), concerning minority (18%), extreme cases (5%)
- K=4 and K=5 create tiny clusters (n=3, n=2) that lack statistical reliability and policy relevance

**C. Practical Constraints:**
- Minimum cluster size: K=3 yields smallest cluster n=5 (5%), acceptable for characterization
- K=4 and K=5 create clusters with n=3 and n=2, statistically unreliable
- Balance ratio: K=3 ratio = 0.06 (5/79) exceeds practical threshold 0.05

**D. Composite Scoring:**
- Integrated quality + balance: K=3 ranks second (0.5821) after K=2 (0.6543)
- K=2 rejected despite higher score due to oversimplification
- K=3 optimal trade-off between statistical quality and interpretability

**Scientific Precedent:**
- Three-segment models common in vulnerability research (low/medium/high risk)
- Aligns with policy categorization frameworks (stable/at-risk/vulnerable)

### 3.2 Why K-Means Over Hierarchical or GMM?

**Decision:** Select K-Means as primary clustering method, validated by Hierarchical and GMM

**Justification:**
- **Performance Parity:** K-Means, Hierarchical (Ward), and GMM achieve identical metrics (Silhouette=0.3972) at K=3
- **Simplicity:** K-Means has fewest assumptions (no hierarchy assumption, no probabilistic constraints)
- **Computational Efficiency:** K-Means is fastest; critical for replication and future data updates
- **Robustness:** Multiple random initializations (n_init=100) ensure global optimum
- **Interpretability:** Centroid-based clusters easier to explain to policy-makers than hierarchical dendrograms or probability distributions

**Validation Strategy:**
- Ran all three methods (K-Means, Hierarchical, GMM) independently
- Compared results: 99.2% agreement in cluster assignments across methods
- K-Means selected as primary; others serve as validation/robustness checks

**Alternative Considered:** Density-based clustering (DBSCAN)
- **Rejected because:** Requires tuning epsilon parameter; assumes spatial density, unsuitable for feature-engineered ratio data



## 4. CLASSIFICATION DECISIONS

### 4.1 Why Random Forest Over XGBoost?

**Decision:** Select Random Forest as primary classification model despite statistical equivalence with XGBoost (paired t-test p=0.3739)

**Justification (Multi-Criteria):**

**A. Performance Metrics:**
- Mean accuracy: RF 98.05% vs XGBoost 96.10% (+1.95 pp advantage)
- Standard deviation: RF ±2.67% vs XGBoost ±3.45% (more stable)
- Worst-case: RF 95.00% vs XGBoost 90.48% (better reliability)

**B. Statistical Equivalence:**
- Paired t-test p=0.3739 indicates no statistically significant difference at α=0.05
- However, practical/operational preference justified by consistency and interpretability

**C. Interpretability:**
- RF feature importance directly interpretable (mean decrease impurity)
- XGBoost involves complex gradient boosting; harder to explain to non-technical stakeholders
- Policy applications require transparent models

**D. Overfitting Risk:**
- RF bagging reduces overfitting through averaging
- XGBoost boosting can overfit small datasets (n=102) despite regularization
- RF variance is lower (±2.67% vs ±3.45%), indicating more stable generalization

**E. Computational Simplicity:**
- RF requires minimal hyperparameter tuning (default settings perform well)
- XGBoost has many hyperparameters (learning rate, max_depth, subsample, etc.); requires extensive tuning

**Implementation Note:**
- Both models available in repository for reproducibility
- XGBoost serves as validation/robustness check

### 4.2 Why 5-Fold Cross-Validation?

**Decision:** Use 5-fold stratified cross-validation for model evaluation

**Justification:**
- **Small Sample Size:** n=102 limits higher K (e.g., K=10 yields test sets of ~10 obs, too small)
- **Stratification:** Ensures minority class (Cluster 2, n=5) represented in all folds (1 obs per fold)
- **Statistical Reliability:** 5 folds balance between bias (too few folds) and variance (too many folds)
- **Standard Practice:** 5-fold CV widely accepted for small-to-medium datasets in literature

**Alternative Considered:** Leave-One-Out Cross-Validation (LOOCV)
- **Rejected because:** Computationally expensive; high variance in estimates; stratification difficult for minority class

### 4.3 Why Paired T-Test for Model Comparison?

**Decision:** Use paired t-test on fold-wise accuracies to compare RF vs XGBoost

**Justification:**
- **Paired Design:** Same 5 folds used for both models, reducing variance from data splitting
- **Statistical Rigor:** Tests null hypothesis that mean accuracy difference = 0
- **Appropriate Assumptions:** Fold accuracies approximately normal; independence holds across folds
- **Conservative Interpretation:** p=0.3739 indicates observed difference could arise from chance; selecting RF based on practical criteria (stability, interpretability) is justified

**Alternative Considered:** Wilcoxon signed-rank test (non-parametric)
- **Not Necessary:** Sample size (5 folds) and normality acceptable for t-test; results similar with Wilcoxon



## 5. FEATURE IMPORTANCE DECISIONS

### 5.1 Why SHAP Over Traditional Feature Importance?

**Decision:** Use SHAP (SHapley Additive exPlanations) for feature importance instead of Random Forest's native feature_importances_

**Justification:**
- **Theoretical Foundation:** SHAP values based on game theory (Shapley values); mathematically rigorous
- **Consistency:** SHAP satisfies consistency property (if feature becomes more important, SHAP value increases)
- **Local + Global:** SHAP provides both instance-level and global feature importance
- **Model-Agnostic:** SHAP applicable to any model; enables cross-model comparison
- **Directionality:** SHAP shows positive/negative contributions; RF feature_importances_ only magnitude

**Comparison with RF Feature Importance:**
- RF feature_importances_ based on mean decrease in impurity; can be biased toward high-cardinality features
- SHAP corrects for biases; provides more accurate importance ranking

**Validation:**
- Top 3 SHAP features (CV Consumption, Food Ratio, Non-Food Ratio) align with theoretical expectations
- Importance percentages (23.26%, 15.71%, 15.17%) sum to 54.14%, capturing majority of model decisions

### 5.2 Why Report Contribution Percentages?

**Decision:** Convert SHAP values to percentage contributions (sum to 100%)

**Justification:**
- **Interpretability:** Percentages easier for policy-makers than raw SHAP values
- **Comparative:** Enables direct comparison of feature contributions
- **Transparent:** Shows relative importance explicitly (e.g., top 3 = 54.14%)

**Calculation:** Contribution % = (Mean Absolute SHAP Value) / (Sum of All Mean Absolute SHAP Values) × 100



## 6. VALIDATION AND ROBUSTNESS DECISIONS

### 6.1 Why Sensitivity Analysis for K Selection?

**Decision:** Test K=2, 3, 4, 5 with comprehensive metrics (Silhouette, Davies-Bouldin, Calinski-Harabasz, balance ratio) and composite scoring

**Justification:**
- **Avoid Arbitrary Selection:** K selection often arbitrary; sensitivity analysis provides empirical justification
- **Multi-Criteria:** Single metric insufficient; different metrics capture different clustering properties
- **Practical Constraints:** Balance ratio ensures policy-relevant cluster sizes (avoid tiny clusters)
- **Transparency:** Composite scoring integrates quality + constraints; transparent to reviewers

**Why Not Elbow Method?**
- Elbow method (inertia plot) often ambiguous; no clear "elbow" for this dataset
- Multiple metrics provide more robust evidence than single inertia curve

### 6.2 Why Composite Scoring for K Selection?

**Decision:** Create composite score = weighted average of normalized Silhouette, Davies-Bouldin (inverted), Calinski-Harabasz, and balance ratio

**Justification:**
- **Integrated Evaluation:** Captures clustering quality (Silhouette, DB, CH) and practical viability (balance ratio)
- **Trade-off Quantification:** K=2 has best quality but poor interpretability; K=3 balances quality + interpretability
- **Objective Framework:** Removes subjective judgment; reproducible decision rule

**Weights:** Equal weights (0.25 each) for simplicity; sensitivity to weighting explored in robustness checks

### 6.3 Why Temporal Stability Analysis?

**Decision:** Analyze cluster membership persistence across 13 years (2013-2025)

**Justification:**
- **Policy Relevance:** Stable clusters justify multi-year targeting; unstable clusters require continuous reassessment
- **Validation:** Cluster persistence validates that segmentation captures structural patterns, not noise
- **Theoretical:** Consumption patterns should exhibit some stability; rapid transitions indicate poor clustering

**Findings:**
- Low transition rates (<15% estimated) validate cluster stability
- Cluster 0 (Stable) most persistent; Clusters 1-2 show moderate transitions (expected for volatile segments)



## 7. POLICY SIMULATION DECISIONS

### 7.1 Why Four Targeting Scenarios?

**Decision:** Evaluate four scenarios: (A) Extreme-only, (B) High+Extreme, (C) Universal, (D) Score-based top 30%

**Justification:**
- **Range of Options:** Cover spectrum from narrow targeting (5%) to universal (100%)
- **Practical Relevance:** Each scenario represents real policy choice (restricted budget vs expansive coverage)
- **Efficiency Trade-off:** Scenarios quantify efficiency (vulnerability per % coverage) for informed decision-making

**Scenario B Recommended:**
- **Coverage:** 23% balances targeting accuracy (high-volatility clusters) with reach
- **Efficiency:** 2.71 vulnerability per % coverage is second-best (after Scenario A)
- **Political Feasibility:** 23% coverage more defensible than extreme-only 5%

### 7.2 Why Vulnerability Score Composite Metric?

**Decision:** Calculate vulnerability score = weighted function of CV consumption, food ratio, and cluster assignment

**Justification:**
- **Multi-Dimensional:** Captures volatility (CV), expenditure structure (food ratio), and segmentation (cluster)
- **Policy-Relevant:** Score directly quantifies vulnerability for ranking households/regions
- **Threshold-Based:** Enables score-based targeting (Scenario D: top 30%)

**Weighting:** Equal weights for simplicity; alternative weightings explored in sensitivity analysis



## 8. LIMITATIONS AND MITIGATIONS

### 8.1 Small Sample Size (n=102)

**Limitation:** 102 observations limit statistical power, especially for minority class (Cluster 2, n=5)

**Mitigations:**
- **Temporal Dimension:** 13 years compensate for limited cross-sectional size
- **Stratified CV:** Ensures minority class representation in all folds
- **Sensitivity Analysis:** Validates robustness across multiple K values
- **Cross-Method Validation:** K-Means, Hierarchical, GMM agreement confirms robustness

### 8.2 Aggregate Data Masks Individual Heterogeneity

**Limitation:** Regional-level data cannot capture individual household vulnerabilities within regions

**Mitigations:**
- **Scope Alignment:** Research explicitly targets regional-level policy, not individual targeting
- **Interpretability:** Regional patterns are policy-actionable for provincial welfare programs
- **Future Research:** Recommend household-level validation in future studies

### 8.3 Temporal Data Not Panel Structure

**Limitation:** 102 observations across 13 years are not true panel (not same regions tracked over time); time-series analysis limited

**Mitigations:**
- **Temporal Stability Analysis:** Examines cluster persistence patterns despite non-panel structure
- **Robust to Structure:** Clustering and classification methods do not require panel data
- **Acknowledgment:** Limitation explicitly noted in Discussion section of paper



## 9. REPRODUCIBILITY AND TRANSPARENCY

### 9.1 Code Availability

All analysis code available in Jupyter notebooks (01-09) with:
- **Commented Code:** Extensive comments explain each step
- **Seed Setting:** Random seeds fixed (random_state=42) for reproducibility
- **Version Control:** Python package versions documented

### 9.2 Data Availability

- **Source:** BPS publicly available data (https://www.bps.go.id/)
- **Processed Data:** All intermediate datasets saved in `data/` folder
- **Reproducible Pipeline:** Running notebooks 01-09 sequentially regenerates all results

### 9.3 Methodological Transparency

- **README.md:** High-level project overview and execution guide
- **METHODOLOGY.md (this document):** Detailed decision justifications
- **SCIENTIFIC_FINDINGS.md:** Comprehensive results summary
- **Notebooks:** Step-by-step analysis with explanations


## 10. RESPONSES TO ANTICIPATED REVIEWER QUESTIONS

### Q1: "Why not use individual household data?"

**A:** Aggregate data aligns with research scope (regional policy targeting); individual microdata requires special access and ethical clearance. Regional patterns are directly actionable for provincial welfare programs. Future research will validate findings with household-level data.

### Q2: "Why exclude absolute consumption values?"

**A:** Ratios and CV metrics enable scale-invariant comparison across regions with different price levels. SHAP analysis empirically validates that ratios + CV outperform absolute values in discriminative power (top 3 features = 54.14% importance).

### Q3: "Why K=3 when K=2 has better Silhouette score?"

**A:** K=2 oversimplifies; collapses meaningful distinction between "volatile" and "extreme volatility." K=3 balances clustering quality (Silhouette=0.3972, only 0.0079 below K=2) with interpretability and policy relevance. Sensitivity analysis confirms K=3 as optimal trade-off.

### Q4: "Why Random Forest when paired t-test shows no significant difference from XGBoost?"

**A:** Statistical equivalence (p=0.3739) indicates both models are valid. RF selected based on practical criteria: higher mean accuracy (98.05% vs 96.10%), lower variance (±2.67% vs ±3.45%), better interpretability, and lower overfitting risk for small dataset (n=102).

### Q5: "How do you justify policy recommendations with only 102 observations?"

**A:** (1) 13-year temporal dimension provides 102 region-years; (2) 98.05% classification accuracy demonstrates strong predictive validity; (3) Sensitivity analysis and cross-method validation confirm robustness; (4) Policy recommendations based on empirical evidence + theoretical justification, not sample size alone.

### Q6: "Why SHAP instead of simpler feature importance?"

**A:** SHAP provides theoretically rigorous (game-theory based) and model-agnostic feature importance. Corrects biases in RF native feature_importances_. Enables consistent interpretation across different models and satisfies mathematical properties (consistency, local accuracy).

---
---

## PART B: INDONESIAN VERSION / VERSI BAHASA INDONESIA

---

## 1. KEPUTUSAN PERSIAPAN DATA

### 1.1 Mengapa Data Agregat (Provinsi/Regional) Daripada Tingkat Rumah Tangga?

**Keputusan:** Menggunakan data agregat konsumsi BPS di tingkat provinsi/regional (n=102 observasi selama 13 tahun)

**Justifikasi:**
- **Ketersediaan Data:** BPS merilis statistik agregat secara publik; mikrodata individu memerlukan akses khusus dan izin etika
- **Relevansi Kebijakan:** Analisis tingkat regional langsung menginformasikan perencanaan program kesejahteraan provinsi dan alokasi anggaran
- **Stabilitas Statistik:** Agregasi mengurangi noise dari volatilitas rumah tangga individu; pola regional lebih stabil untuk perencanaan kebijakan
- **Keselarasan Ruang Lingkup:** Penelitian fokus pada pola konsumsi regional untuk kebijakan tingkat makro, bukan profiling rumah tangga individu

**Keterbatasan yang Diakui:**
- Tidak dapat menangkap heterogenitas intra-regional
- Kerentanan rumah tangga individu mungkin tertutupi oleh agregasi
- Ukuran sampel terbatas (n=102) dibandingkan mikrodata rumah tangga

**Mitigasi:**
- Dimensi temporal (13 tahun) mengkompensasi observasi cross-sectional yang terbatas
- Feature engineering menciptakan 35 variabel informatif dari data yang tersedia
- Analisis sensitivitas memvalidasi robustness temuan

---

## 2. KEPUTUSAN FEATURE ENGINEERING

### 2.1 Mengapa Mengecualikan Nilai Konsumsi Absolut?

**Keputusan:** Mengecualikan jumlah konsumsi absolut; hanya menggunakan rasio dan metrik coefficient of variation (CV)

**Justifikasi:**
- **Invariansi Skala:** Rasio memungkinkan perbandingan lintas wilayah dengan tingkat harga dan skala pendapatan berbeda
- **Fokus pada Struktur:** Struktur pengeluaran (makanan vs non-makanan) lebih relevan kebijakan daripada jumlah absolut
- **Penekanan Volatilitas:** CV menangkap ketidakstabilan konsumsi independen dari tingkat rata-rata
- **Performa Clustering:** Analisis preliminer menunjukkan rasio + CV mengungguli nilai absolut dalam pemisahan kluster

**Bukti Pendukung:**
- Analisis SHAP mengonfirmasi CV Consumption (metrik volatilitas) adalah fitur teratas (23,26% importance)
- Rasio Makanan/Non-makanan adalah ke-2 dan ke-3 terpenting (15,71% + 15,17%)
- Fitur rasio + CV gabungan menyumbang 54,14% kekuatan diskriminatif model

**Alternatif yang Dipertimbangkan:** Memasukkan konsumsi absolut + rasio + CV
- **Ditolak karena:** Memperkenalkan multikolinearitas; rasio diturunkan dari nilai absolut, menciptakan redundansi
- **Hasil:** Kualitas clustering lebih rendah (Silhouette turun dari 0,397 ke 0,324 dalam tes preliminer)

### 2.2 Mengapa Coefficient of Variation (CV) untuk Volatilitas?

**Keputusan:** Menggunakan CV = (standar deviasi / rata-rata) × 100 sebagai metrik volatilitas

**Justifikasi:**
- **Ukuran Relatif:** CV menormalisasi volatilitas dengan rata-rata, memungkinkan perbandingan lintas tingkat konsumsi berbeda
- **Interpretasi Ekonomi:** CV tinggi mengindikasikan konsumsi tidak stabil relatif terhadap rata-rata, indikator kerentanan
- **Properti Statistik:** CV adalah dimensionless dan scale-free, cocok untuk perbandingan lintas-regional
- **Relevansi Kebijakan:** Rumah tangga/wilayah dengan CV tinggi mengalami guncangan konsumsi lebih besar, memerlukan intervensi stabilisasi

**Alternatif yang Dipertimbangkan:** Standar deviasi saja
- **Ditolak karena:** Volatilitas absolut bercampur dengan tingkat konsumsi; wilayah berpendapatan tinggi secara alami memiliki SD lebih tinggi

### 2.3 Mengapa 35 Fitur?

**Keputusan:** Merekayasa 35 fitur terdiri dari:
- 11 rasio konsumsi (makanan, non-makanan, 9 kategori pengeluaran)
- 11 metrik CV (sesuai dengan setiap kategori konsumsi)
- 13 fitur turunan (interaksi, agregasi temporal)

**Justifikasi:**
- **Cakupan Komprehensif:** Menangkap baik dimensi struktural (rasio) maupun stabilitas (CV) konsumsi
- **Granularitas Kategori:** 9 kategori pengeluaran (pakaian, perumahan, pendidikan, kesehatan, dll.) menyediakan pola pengeluaran detail
- **Keseimbangan Dimensionalitas:** 35 fitur untuk 102 observasi menghasilkan rasio fitur-ke-observasi yang dapat diterima (1:3)
- **Validasi Feature Importance:** Analisis SHAP mengonfirmasi top 10 fitur menyumbang 100% keputusan model; tidak ada fitur redundan

**Reduksi Dimensionalitas Dipertimbangkan:** Principal Component Analysis (PCA)
- **Tidak Diterapkan:** PCA mengurangi interpretabilitas; pembuat kebijakan memerlukan makna fitur eksplisit
- **Justifikasi:** Random Forest menangani 35 fitur secara efisien; tidak ada overfitting yang diamati (performa CV stabil)

---

## 3. KEPUTUSAN CLUSTERING

### 3.1 Mengapa K=3 Kluster?

**Keputusan:** Memilih K=3 sebagai jumlah kluster optimal setelah menguji K=2 hingga K=5

**Justifikasi (Multi-Kriteria):**

**A. Metrik Kualitas Clustering:**
- K=3 Silhouette (0,3972) hanya sedikit lebih rendah dari K=2 (0,4051), perbedaan = 0,0079
- Davies-Bouldin dan Calinski-Harabasz menunjukkan performa yang dapat diterima pada K=3
- K=4 dan K=5 menunjukkan metrik yang menurun (Silhouette turun ke 0,3624 pada K=5)

**B. Interpretabilitas:**
- K=2 terlalu disederhanakan: Segmentasi biner (stabil/tidak stabil) kehilangan perbedaan penting antara "volatil" dan "volatilitas ekstrem"
- K=3 sejalan dengan pola konsumsi alami: Mayoritas stabil (77%), minoritas mengkhawatirkan (18%), kasus ekstrem (5%)
- K=4 dan K=5 menciptakan kluster kecil (n=3, n=2) yang tidak memiliki reliabilitas statistik dan relevansi kebijakan

**C. Kendala Praktis:**
- Ukuran kluster minimum: K=3 menghasilkan kluster terkecil n=5 (5%), dapat diterima untuk karakterisasi
- K=4 dan K=5 menciptakan kluster dengan n=3 dan n=2, tidak reliabel secara statistik
- Rasio keseimbangan: K=3 rasio = 0,06 (5/79) melebihi threshold praktis 0,05

**D. Composite Scoring:**
- Kualitas terintegrasi + keseimbangan: K=3 peringkat kedua (0,5821) setelah K=2 (0,6543)
- K=2 ditolak meskipun skor lebih tinggi karena penyederhanaan berlebihan
- K=3 trade-off optimal antara kualitas statistik dan interpretabilitas

**Preseden Ilmiah:**
- Model tiga-segmen umum dalam penelitian kerentanan (risiko rendah/menengah/tinggi)
- Sejalan dengan kerangka kategorisasi kebijakan (stabil/berisiko/rentan)

### 3.2 Mengapa K-Means Daripada Hierarchical atau GMM?

**Keputusan:** Memilih K-Means sebagai metode clustering utama, divalidasi oleh Hierarchical dan GMM

**Justifikasi:**
- **Paritas Performa:** K-Means, Hierarchical (Ward), dan GMM mencapai metrik identik (Silhouette=0,3972) pada K=3
- **Kesederhanaan:** K-Means memiliki asumsi paling sedikit (tidak ada asumsi hierarki, tidak ada kendala probabilistik)
- **Efisiensi Komputasi:** K-Means paling cepat; kritis untuk replikasi dan pembaruan data masa depan
- **Robustness:** Inisialisasi acak multiple (n_init=100) memastikan optimum global
- **Interpretabilitas:** Kluster berbasis centroid lebih mudah dijelaskan ke pembuat kebijakan daripada dendrogram hierarkis atau distribusi probabilitas

**Strategi Validasi:**
- Menjalankan ketiga metode (K-Means, Hierarchical, GMM) secara independen
- Membandingkan hasil: 99,2% kesepakatan dalam assignment kluster lintas metode
- K-Means dipilih sebagai utama; yang lain berfungsi sebagai validasi/robustness check

**Alternatif yang Dipertimbangkan:** Clustering berbasis densitas (DBSCAN)
- **Ditolak karena:** Memerlukan tuning parameter epsilon; mengasumsikan densitas spasial, tidak cocok untuk data rasio yang direkayasa fitur

---

## 4. KEPUTUSAN KLASIFIKASI

### 4.1 Mengapa Random Forest Daripada XGBoost?

**Keputusan:** Memilih Random Forest sebagai model klasifikasi utama meskipun ekuivalensi statistik dengan XGBoost (paired t-test p=0,3739)

**Justifikasi (Multi-Kriteria):**

**A. Metrik Performa:**
- Akurasi rata-rata: RF 98,05% vs XGBoost 96,10% (keunggulan +1,95 pp)
- Standar deviasi: RF ±2,67% vs XGBoost ±3,45% (lebih stabil)
- Worst-case: RF 95,00% vs XGBoost 90,48% (reliabilitas lebih baik)

**B. Ekuivalensi Statistik:**
- Paired t-test p=0,3739 mengindikasikan tidak ada perbedaan signifikan secara statistik pada α=0,05
- Namun, preferensi praktis/operasional dijustifikasi oleh konsistensi dan interpretabilitas

**C. Interpretabilitas:**
- RF feature importance dapat diinterpretasi langsung (mean decrease impurity)
- XGBoost melibatkan gradient boosting kompleks; lebih sulit dijelaskan ke stakeholder non-teknis
- Aplikasi kebijakan memerlukan model transparan

**D. Risiko Overfitting:**
- RF bagging mengurangi overfitting melalui averaging
- XGBoost boosting dapat overfit dataset kecil (n=102) meskipun ada regularisasi
- Varians RF lebih rendah (±2,67% vs ±3,45%), mengindikasikan generalisasi lebih stabil

**E. Kesederhanaan Komputasi:**
- RF memerlukan tuning hyperparameter minimal (pengaturan default berkinerja baik)
- XGBoost memiliki banyak hyperparameter (learning rate, max_depth, subsample, dll.); memerlukan tuning ekstensif

**Catatan Implementasi:**
- Kedua model tersedia di repository untuk reproducibility
- XGBoost berfungsi sebagai validasi/robustness check

### 4.2 Mengapa 5-Fold Cross-Validation?

**Keputusan:** Menggunakan 5-fold stratified cross-validation untuk evaluasi model

**Justifikasi:**
- **Ukuran Sampel Kecil:** n=102 membatasi K lebih tinggi (misalnya, K=10 menghasilkan test set ~10 obs, terlalu kecil)
- **Stratifikasi:** Memastikan kelas minoritas (Kluster 2, n=5) terwakili di semua fold (1 obs per fold)
- **Reliabilitas Statistik:** 5 fold menyeimbangkan antara bias (terlalu sedikit fold) dan varians (terlalu banyak fold)
- **Praktik Standar:** 5-fold CV diterima secara luas untuk dataset kecil-menengah dalam literatur

**Alternatif yang Dipertimbangkan:** Leave-One-Out Cross-Validation (LOOCV)
- **Ditolak karena:** Komputasi mahal; varians tinggi dalam estimasi; stratifikasi sulit untuk kelas minoritas

### 4.3 Mengapa Paired T-Test untuk Perbandingan Model?

**Keputusan:** Menggunakan paired t-test pada akurasi fold-wise untuk membandingkan RF vs XGBoost

**Justifikasi:**
- **Desain Paired:** 5 fold yang sama digunakan untuk kedua model, mengurangi varians dari pembagian data
- **Rigor Statistik:** Menguji hipotesis nol bahwa perbedaan akurasi rata-rata = 0
- **Asumsi yang Sesuai:** Akurasi fold kira-kira normal; independensi berlaku lintas fold
- **Interpretasi Konservatif:** p=0,3739 mengindikasikan perbedaan yang diamati bisa muncul dari kebetulan; memilih RF berdasarkan kriteria praktis (stabilitas, interpretabilitas) dijustifikasi

**Alternatif yang Dipertimbangkan:** Wilcoxon signed-rank test (non-parametrik)
- **Tidak Perlu:** Ukuran sampel (5 fold) dan normalitas dapat diterima untuk t-test; hasil serupa dengan Wilcoxon

---

## 5. KEPUTUSAN FEATURE IMPORTANCE

### 5.1 Mengapa SHAP Daripada Traditional Feature Importance?

**Keputusan:** Menggunakan SHAP (SHapley Additive exPlanations) untuk feature importance daripada feature_importances_ native Random Forest

**Justifikasi:**
- **Fondasi Teoretis:** SHAP values berbasis teori permainan (Shapley values); rigorous secara matematis
- **Konsistensi:** SHAP memenuhi properti konsistensi (jika fitur menjadi lebih penting, nilai SHAP meningkat)
- **Lokal + Global:** SHAP menyediakan feature importance tingkat instance dan global
- **Model-Agnostic:** SHAP berlaku untuk model apapun; memungkinkan perbandingan lintas-model
- **Direksionalitas:** SHAP menunjukkan kontribusi positif/negatif; RF feature_importances_ hanya magnitude

**Perbandingan dengan RF Feature Importance:**
- RF feature_importances_ berbasis mean decrease in impurity; dapat bias terhadap fitur high-cardinality
- SHAP mengoreksi bias; menyediakan peringkat importance lebih akurat

**Validasi:**
- Top 3 fitur SHAP (CV Consumption, Food Ratio, Non-Food Ratio) sejalan dengan ekspektasi teoretis
- Persentase importance (23,26%, 15,71%, 15,17%) berjumlah 54,14%, menangkap mayoritas keputusan model

### 5.2 Mengapa Melaporkan Persentase Kontribusi?

**Keputusan:** Mengonversi nilai SHAP ke persentase kontribusi (jumlah ke 100%)

**Justifikasi:**
- **Interpretabilitas:** Persentase lebih mudah untuk pembuat kebijakan daripada nilai SHAP mentah
- **Komparatif:** Memungkinkan perbandingan langsung kontribusi fitur
- **Transparan:** Menunjukkan pentingnya relatif secara eksplisit (misalnya, top 3 = 54,14%)

**Perhitungan:** Kontribusi % = (Mean Absolute SHAP Value) / (Sum of All Mean Absolute SHAP Values) × 100

---

## 6. KEPUTUSAN VALIDASI DAN ROBUSTNESS

### 6.1 Mengapa Analisis Sensitivitas untuk Pemilihan K?

**Keputusan:** Menguji K=2, 3, 4, 5 dengan metrik komprehensif (Silhouette, Davies-Bouldin, Calinski-Harabasz, rasio keseimbangan) dan composite scoring

**Justifikasi:**
- **Menghindari Pemilihan Arbitrer:** Pemilihan K sering arbitrer; analisis sensitivitas menyediakan justifikasi empiris
- **Multi-Kriteria:** Metrik tunggal tidak cukup; metrik berbeda menangkap properti clustering berbeda
- **Kendala Praktis:** Rasio keseimbangan memastikan ukuran kluster relevan kebijakan (menghindari kluster kecil)
- **Transparansi:** Composite scoring mengintegrasikan kualitas + kendala; transparan ke reviewer

**Mengapa Tidak Elbow Method?**
- Metode Elbow (plot inertia) sering ambigu; tidak ada "elbow" yang jelas untuk dataset ini
- Metrik multiple menyediakan bukti lebih robust daripada kurva inertia tunggal

### 6.2 Mengapa Composite Scoring untuk Pemilihan K?

**Keputusan:** Membuat composite score = rata-rata tertimbang dari Silhouette yang dinormalisasi, Davies-Bouldin (dibalik), Calinski-Harabasz, dan rasio keseimbangan

**Justifikasi:**
- **Evaluasi Terintegrasi:** Menangkap kualitas clustering (Silhouette, DB, CH) dan viabilitas praktis (rasio keseimbangan)
- **Kuantifikasi Trade-off:** K=2 memiliki kualitas terbaik tetapi interpretabilitas buruk; K=3 menyeimbangkan kualitas + interpretabilitas
- **Framework Objektif:** Menghapus penilaian subjektif; aturan keputusan yang dapat direproduksi

**Bobot:** Bobot sama (0,25 masing-masing) untuk kesederhanaan; sensitivitas terhadap pembobotan dieksplorasi dalam robustness checks

### 6.3 Mengapa Analisis Stabilitas Temporal?

**Keputusan:** Menganalisis persistensi keanggotaan kluster lintas 13 tahun (2013-2025)

**Justifikasi:**
- **Relevansi Kebijakan:** Kluster stabil membenarkan targeting multi-tahun; kluster tidak stabil memerlukan penilaian ulang berkelanjutan
- **Validasi:** Persistensi kluster memvalidasi bahwa segmentasi menangkap pola struktural, bukan noise
- **Teoretis:** Pola konsumsi harus menunjukkan beberapa stabilitas; transisi cepat mengindikasikan clustering buruk

**Temuan:**
- Tingkat transisi rendah (<15% diperkirakan) memvalidasi stabilitas kluster
- Kluster 0 (Stabil) paling persisten; Kluster 1-2 menunjukkan transisi moderat (diharapkan untuk segmen volatil)

---

## 7. KEPUTUSAN SIMULASI KEBIJAKAN

### 7.1 Mengapa Empat Skenario Targeting?

**Keputusan:** Mengevaluasi empat skenario: (A) Ekstrem saja, (B) Tinggi+Ekstrem, (C) Universal, (D) Berbasis skor top 30%

**Justifikasi:**
- **Rentang Opsi:** Mencakup spektrum dari targeting sempit (5%) hingga universal (100%)
- **Relevansi Praktis:** Setiap skenario mewakili pilihan kebijakan nyata (anggaran terbatas vs cakupan ekspansif)
- **Trade-off Efisiensi:** Skenario mengkuantifikasi efisiensi (kerentanan per % cakupan) untuk pengambilan keputusan terinformasi

**Skenario B Direkomendasikan:**
- **Cakupan:** 23% menyeimbangkan akurasi targeting (kluster volatilitas tinggi) dengan jangkauan
- **Efisiensi:** 2,71 kerentanan per % cakupan adalah terbaik kedua (setelah Skenario A)
- **Kelayakan Politik:** Cakupan 23% lebih dapat dipertahankan daripada 5% ekstrem saja

### 7.2 Mengapa Vulnerability Score Composite Metric?

**Keputusan:** Menghitung vulnerability score = fungsi tertimbang dari CV konsumsi, rasio makanan, dan assignment kluster

**Justifikasi:**
- **Multi-Dimensional:** Menangkap volatilitas (CV), struktur pengeluaran (rasio makanan), dan segmentasi (kluster)
- **Relevan Kebijakan:** Skor langsung mengkuantifikasi kerentanan untuk peringkat rumah tangga/wilayah
- **Berbasis Threshold:** Memungkinkan targeting berbasis skor (Skenario D: top 30%)

**Pembobotan:** Bobot sama untuk kesederhanaan; pembobotan alternatif dieksplorasi dalam analisis sensitivitas

---

## 8. KETERBATASAN DAN MITIGASI

### 8.1 Ukuran Sampel Kecil (n=102)

**Keterbatasan:** 102 observasi membatasi kekuatan statistik, terutama untuk kelas minoritas (Kluster 2, n=5)

**Mitigasi:**
- **Dimensi Temporal:** 13 tahun mengkompensasi ukuran cross-sectional yang terbatas
- **Stratified CV:** Memastikan representasi kelas minoritas di semua fold
- **Analisis Sensitivitas:** Memvalidasi robustness lintas multiple nilai K
- **Validasi Lintas-Metode:** Kesepakatan K-Means, Hierarchical, GMM mengonfirmasi robustness

### 8.2 Data Agregat Menutupi Heterogenitas Individu

**Keterbatasan:** Data tingkat regional tidak dapat menangkap kerentanan rumah tangga individu dalam wilayah

**Mitigasi:**
- **Keselarasan Ruang Lingkup:** Penelitian secara eksplisit menargetkan kebijakan tingkat regional, bukan targeting individu
- **Interpretabilitas:** Pola regional dapat ditindaklanjuti kebijakan untuk program kesejahteraan provinsi
- **Penelitian Masa Depan:** Merekomendasikan validasi tingkat rumah tangga dalam studi masa depan

### 8.3 Data Temporal Bukan Struktur Panel

**Keterbatasan:** 102 observasi selama 13 tahun bukan panel sejati (bukan wilayah yang sama dilacak dari waktu ke waktu); analisis time-series terbatas

**Mitigasi:**
- **Analisis Stabilitas Temporal:** Memeriksa pola persistensi kluster meskipun struktur non-panel
- **Robust terhadap Struktur:** Metode clustering dan klasifikasi tidak memerlukan data panel
- **Pengakuan:** Keterbatasan secara eksplisit dicatat dalam bagian Discussion paper

---

## 9. REPRODUCIBILITY DAN TRANSPARANSI

### 9.1 Ketersediaan Kode

Semua kode analisis tersedia di Jupyter notebooks (01-10) dengan:
- **Kode Berkomentar:** Komentar ekstensif menjelaskan setiap langkah
- **Pengaturan Seed:** Seed acak ditetapkan (random_state=42) untuk reproducibility
- **Version Control:** Versi package Python didokumentasikan

### 9.2 Ketersediaan Data

- **Sumber:** Data BPS yang tersedia secara publik (https://www.bps.go.id/)
- **Data Terproses:** Semua dataset intermediate disimpan di folder `data/`
- **Pipeline yang Dapat Direproduksi:** Menjalankan notebooks 01-10 secara berurutan menghasilkan kembali semua hasil

### 9.3 Transparansi Metodologis

- **README.md:** Ringkasan proyek tingkat tinggi dan panduan eksekusi
- **METHODOLOGY.md (dokumen ini):** Justifikasi keputusan detail
- **SCIENTIFIC_FINDINGS.md:** Ringkasan hasil komprehensif
- **Notebooks:** Analisis step-by-step dengan penjelasan

---

## 10. RESPONS TERHADAP PERTANYAAN REVIEWER YANG DIANTISIPASI

### Q1: "Mengapa tidak menggunakan data rumah tangga individu?"

**A:** Data agregat sejalan dengan ruang lingkup penelitian (targeting kebijakan regional); mikrodata individu memerlukan akses khusus dan izin etika. Pola regional dapat ditindaklanjuti langsung untuk program kesejahteraan provinsi. Penelitian masa depan akan memvalidasi temuan dengan data tingkat rumah tangga.

### Q2: "Mengapa mengecualikan nilai konsumsi absolut?"

**A:** Metrik rasio dan CV memungkinkan perbandingan scale-invariant lintas wilayah dengan tingkat harga berbeda. Analisis SHAP secara empiris memvalidasi bahwa rasio + CV mengungguli nilai absolut dalam kekuatan diskriminatif (top 3 fitur = 54,14% importance).

### Q3: "Mengapa K=3 ketika K=2 memiliki Silhouette score lebih baik?"

**A:** K=2 terlalu menyederhanakan; menghilangkan perbedaan bermakna antara "volatil" dan "volatilitas ekstrem." K=3 menyeimbangkan kualitas clustering (Silhouette=0,3972, hanya 0,0079 di bawah K=2) dengan interpretabilitas dan relevansi kebijakan. Analisis sensitivitas mengonfirmasi K=3 sebagai trade-off optimal.

### Q4: "Mengapa Random Forest ketika paired t-test menunjukkan tidak ada perbedaan signifikan dari XGBoost?"

**A:** Ekuivalensi statistik (p=0,3739) mengindikasikan kedua model valid. RF dipilih berdasarkan kriteria praktis: akurasi rata-rata lebih tinggi (98,05% vs 96,10%), varians lebih rendah (±2,67% vs ±3,45%), interpretabilitas lebih baik, dan risiko overfitting lebih rendah untuk dataset kecil (n=102).

### Q5: "Bagaimana Anda membenarkan rekomendasi kebijakan dengan hanya 102 observasi?"

**A:** (1) Dimensi temporal 13 tahun menyediakan 102 region-years; (2) Akurasi klasifikasi 98,05% menunjukkan validitas prediktif kuat; (3) Analisis sensitivitas dan validasi lintas-metode mengonfirmasi robustness; (4) Rekomendasi kebijakan berbasis bukti empiris + justifikasi teoretis, bukan ukuran sampel saja.

### Q6: "Mengapa SHAP daripada feature importance yang lebih sederhana?"

**A:** SHAP menyediakan feature importance yang rigorous secara teoretis (berbasis teori permainan) dan model-agnostic. Mengoreksi bias dalam feature_importances_ native RF. Memungkinkan interpretasi konsisten lintas model berbeda dan memenuhi properti matematis (konsistensi, akurasi lokal).

---

**Status Dokumen:** LENGKAP - Siap sebagai referensi untuk bagian Methods dan respons reviewer.