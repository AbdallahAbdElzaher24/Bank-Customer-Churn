<h1 align="center">🏦 Bank Customer Churn — Clustering, Fuzzy Logic & Genetic Algorithms</h1>

<p align="center">
A data-mining project that segments bank customers, scores their churn risk with fuzzy logic, and picks the best features with a genetic algorithm — then fuses all three into one risk-scoring system.
</p>

<p align="center">
<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white">
<img src="https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white">
<img src="https://img.shields.io/badge/scikit--fuzzy-6A5ACD?style=for-the-badge">
<img src="https://img.shields.io/badge/Genetic_Algorithm-8B0000?style=for-the-badge">
</p>

---

## 🎯 What This Project Does

- 📊 Analyzes **10,000 bank customers** (18 features) to understand & predict **churn (Exited)**
- 🧩 **Task 3 & 4:** Segments customers with **K-Medoids** & **Hierarchical Clustering**
- 🌫️ **Task 5:** Builds a **Fuzzy Logic** system to score churn risk 0–100
- 🧬 **Task 6:** Uses a **Genetic Algorithm** to pick the best feature subset (vs. baseline & PCA)
- 🤖 **Task 7:** Combines all three into a single function that analyzes any customer end-to-end

---

## 🔍 Task 1 — EDA Highlights

- ⚖️ **Class imbalance:** 79.6% retained vs. **20.4% churned**
- 🎂 Churned customers skew **older** (peak 40–55); credit score & tenure aren't strong predictors
- 🇩🇪 **Germany churns hardest (32%)** vs. France (16%) & Spain (17%)
- 👩 **Females churn more (25%)** than males (16%)
- 🛒 Customers with **3–4 products churn 83–100%** — a huge red flag (forced cross-sell backfiring?)
- 🚩 **`complain` ≈ perfectly correlated with `exited` (r≈1.0)** → obvious data leakage, must be excluded from modeling
- 🎯 **Highest-risk segment:** German females (38% churn)

---

## 🧹 Task 2 — Preprocessing

- 🗑️ Dropped `RowNumber`, `CustomerId`, `Surname` (identifiers, no predictive value)
- ✅ No real nulls (imputer run anyway "to do the job" 😄)
- 📦 **IQR capping** (not deletion) on 5 continuous features — kept all 10,000 rows since the data is already imbalanced
  - Outliers found: `age` 3.6%, `numofproducts` 0.6%, `creditscore` 0.15%, rest ~0%
- 🏷️ Label Encoding for binary features, One-Hot Encoding for non-ordinal ones
- ⚖️ `StandardScaler` chosen (better than MinMax for K-Medoids & PCA, which are magnitude-sensitive)

---

## 🧩 Task 3 — K-Medoids Clustering

- 📐 Elbow + Silhouette Score both point to **k = 6** as optimal (Silhouette ≈ 0.074)
- 🗺️ Clusters differ mainly by **geography** and **card type**, not age/gender — the customer base is demographically homogeneous
- 🏷️ Named cluster profiles used later include e.g. *"Active High-Risk"* and *"Dormant Low-Value"*

## 🌳 Task 4 — Hierarchical Clustering

- Compared **single / complete / average** linkage, each with its own best k (2 / 5 / 4) via elbow + silhouette
- Same profiling dashboards (demographic, financial, engagement, churn/loyalty) rebuilt for each linkage method for comparison

---

## 🌫️ Task 5 — Fuzzy Logic Inference System

- Built triangular membership functions over inputs (age, balance, activity, satisfaction) → output: **churn risk score (0–100)**
- Rule base maps customer conditions to an **action recommendation** (e.g. "Send offers, improve engagement, monitor behavior" vs. "No immediate action")
- Ran on real customers — e.g. Customer #2: churn score **60.3/100** → targeted retention action

---

## 🧬 Task 6 — Genetic Algorithm (Feature Selection)

**Setup:** 18-gene binary chromosome, population = 30, generations = 50, crossover = 0.8, mutation = 0.06, elitism = 2, fitness = weighted F1 (Logistic Regression, 3-fold CV)

| Method | Features Used | F1 Score |
|---|---|---|
| Baseline (all features) | 18 | 0.8020 |
| **GA Best 🏆** | **13** | **0.8046** |
| PCA + Logistic Regression | 13 (components) | 0.7398 |

- 🏆 **GA wins:** matches/beats the baseline with **28% fewer features** (`balance`, `hascrcard`, `geography_France`, and 2 card types dropped)
- 📉 PCA underperforms — compressed components lose interpretable signal that GA's raw feature selection keeps

---

## 🤖 Task 7 — Integrated Risk System

One function fuses all three modules for any customer record:
1. **Cluster ID & profile name** (K-Medoids)
2. **Fuzzy churn score + recommended action**
3. **GA-selected features** used for evaluation
4. → **Composite Risk Score** + Final Risk Level (LOW / HIGH)

**Example — Customer #2:** Cluster *"Active High-Risk"*, fuzzy score 60.3/100 → **Composite Risk: 66.2/100 → HIGH RISK** (customer did in fact churn ✅)

---

## 📌 Conclusion

Combining **unsupervised clustering**, **fuzzy expert rules**, and an **evolutionary feature search** gives a churn system that's not just accurate but *explainable* — every customer gets a cluster label, a risk score, and a clear reason why, instead of a black-box prediction. 🏦🔍
