# CS G520 Advanced Data Mining: Midsem Guide

Scope is **what Rathore has actually taught: his four released decks.** Every topic has one source. Unless marked otherwise that source is **his slides**, cited as **D1–D4, slide numbers** (slide = PDF page):

- **D1** = `1.pdf` (Module 1: What is data mining)
- **D2** = `2.pdf` (Data and its types)
- **D3** = `3.pdf` (Data preprocessing & exploration)
- **D4** = `4 Clustering.pdf`

His decks are built from Tan–Steinbach–Kumar (T1 on his slides) and Han–Kamber–Pei 3e (T2), and the worked examples are lifted from them. So the slides *are* the exam source. A book section is named only where the slides are figure-only or send you away ("PCA: Homework!!!").

---

## 0. The exam

| | |
|---|---|
| **Date** | **Not in the handout** ("as per the timetable"). The other midsems fall on 05, 07 and 08 Oct, so expect this one in the same window. **Confirm it and fill it in here** |
| **Format** | **Closed book**, 25%. Surprise quizzes I & II (10%) are closed book too, with no makeup, and use the same material |
| **Syllabus** | Everything in D1–D4. The handout order is Intro → **Classification** → Clustering, but **no classification deck has been released**. He went from preprocessing straight to clustering (the D4 slides are dated 15 Sep). Classification is excluded here; if a deck appears before the midsem, add it |
| **Not in any deck** | EDA summary statistics and visualisation (D3's title says "& Exploration", but there are no slides on it), cluster evaluation (silhouette, SSE-based validity), association rules, LSH, PageRank |

**What closed book plus these decks means.** Expect three kinds of questions:
- **(a) numericals** lifted straight from the slide examples: binning, normalization, k-means iterations, dendrograms, DBSCAN point labels, BIRCH CF vectors
- **(b) "distinguish X vs Y"**: noise vs outlier, stratified vs cluster sampling, hard vs soft, agglomerative vs divisive, MIN vs MAX
- **(c) "when does X fail and what fixes it"**: k-means limitations, DBSCAN on varying density, BIRCH order-sensitivity

Memorize every formula. Practise every numerical once by hand.

**Priority key:** **P1** = near-certain, practise by hand · **P2** = likely short answer · **P3** = one-line definitions only.

---

## 1. Topics, in lecture order

### T1 · What data mining is: the task taxonomy (D1)

- **Read:** D1 slides 4, 7–10, 14–22, 30, 35–36.
- **Exam wants:** Define DM and the KDD pipeline (selection → cleaning/integration → mining → evaluation). Given a scenario, name the task (classification, regression, clustering, association, anomaly detection) and say whether it's predictive or descriptive. List the challenges (scalability, dimensionality, heterogeneity, quality, privacy, streaming).
- **Priority:** P3. Probably 2–3 marks, or a quiz item.
- **Skip:** D1 slides 2–3 (evolution of science), 5–6 (alternative names, origins), 11–13 (BI pyramid, multidimensional view), 23–29 and 32–34 (application galleries).

### T2 · Attribute types (D2)

- **Read:** D2 slides 5–10.
- **Exam wants:** Classify an attribute as nominal, ordinal, interval or ratio using the four properties (distinctness =≠, order <>, addition +−, multiplication ×÷). Know the statistics each type permits (mode → median → mean → geometric mean/ratios), discrete vs continuous, and binary as a special case of discrete. Classic traps: temperature in °C is interval, in K it's ratio, and IQ is interval.
- **Priority:** P1. Easy guaranteed marks.
- **Skip:** D2 slides 2–4 (dataset/object definitions).

### T3 · Types of datasets (D2)

- **Read:** D2 slides 11–23.
- **Exam wants:** Record data (data matrix, document term-vectors, transaction/market-basket), graph data (web links, molecules), ordered data (sequential transactions, genomic sequences, time series, spatio-temporal). Map a scenario to its type and say why it matters for the algorithm.
- **Priority:** P2.
- **Skip:** D2 slide 19 (benzene molecule picture).

### T4 · Data quality: noise, outliers, missing values, duplicates (D3)

- **Read:** D3 slides 2–14.
- **Exam wants:** The six quality dimensions (accuracy, completeness, consistency, timeliness, believability, interpretability), and why quality depends on the user (his "User 1 vs User 2" slide). **Noise vs outlier**: noise is a modification of true values, while an outlier is legitimate data that may itself be the target (fraud, intrusion), and noise can mimic outliers. Also disguised missing data (1 Jan default birthdays) and duplicates from merging sources.
- **Priority:** P1. This is the most likely "distinguish" question.
- **Skip:** None.

### T5 · Data cleaning: missing values and binning (D3)

- **Read:** D3 slides 15–26.
- **Exam wants:** The missing-value strategies ranked by bias (ignore tuple, global constant, local constant, mean/median, class-conditional mean, most-probable value). **Binning numerical:** sort, split into equal-frequency bins, smooth by bin means, medians or boundaries (his price example 4, 8, 15 | 21, 21, 24 | 25, 28, 34). Also smoothing by regression, and clustering-based outlier removal.
- **Priority:** P1. A binning numerical is the single most likely question from D3.
- **Skip:** D3 slide 26 (inconsistent-data remedies, one line).

### T6 · Data integration (D3)

- **Read:** D3 slides 28–32.
- **Exam wants:** The entity identification problem (cust-id ≡ customer_id), data value conflicts (units, representations, J.D. Smith vs John Smith), and redundant or derived attributes found by correlation analysis.
- **Priority:** P2. A short answer.
- **Skip:** None.

### T7 · Transformation: normalization and aggregation (D3)

- **Read:** D3 slides 34–48.
- **Exam wants:** Three formulas you must be able to compute:
  - **Min-max:** v′ = (v − min)/(max − min)·(new_max − new_min) + new_min. His example is age 30, range [10, 80] → 0.28.
  - **Z-score:** (v − μ)/σ. His marks example: 35, 65, 90 with μ = 60, σ = 10 → −2.5, +0.5, +3.0. The robust variant uses mean absolute deviation instead of σ.
  - **Decimal scaling:** v/10^j, with the smallest j such that max|v′| < 1. His example: −986…917 → j = 3.

  Also why you normalize (income drowns age in distance computations), and why aggregation reduces variability (the Australia monthly vs yearly precipitation example).
- **Priority:** P1.
- **Skip:** D3 slide 46 (data-cube picture).

### T8 · Data reduction: histograms and sampling (D3)

- **Read:** D3 slides 50–65.
- **Exam wants:** Why reduce, and dimensionality reduction vs numerosity reduction. Lossless vs lossy compression. Histograms: equal-width vs equal-frequency buckets and singleton buckets (the prices list on slide 54 is a ready numerical). **Sampling:** SRSWOR vs SRSWR, stratified vs cluster (his fruit-box analogy: stratified takes some from *every* group, cluster takes *whole* groups), and that stratified protects small groups in skewed data. Sample-size trade-off (8000 / 2000 / 500 points).
- **Priority:** P1.
- **Skip:** D3 slide 52 ("doctor example"). Wavelets are named on slide 51 but never taught.

### T9 · Feature subset selection and feature creation (D3)

- **Read:** D3 slides 67–72 for everything **except PCA**. For PCA, read **Han–Kamber–Pei 3e §3.4.3** (he set it as "Homework!!!" on slide 72, which usually means it's fair game).
- **Exam wants:** Redundant features (price vs sales tax) vs irrelevant ones (student ID for GPA). Why exhaustive search is infeasible (2ⁿ − 1 subsets), and the greedy methods: stepwise forward selection, backward elimination, decision-tree induction. The three feature-creation routes (extraction: image edges, construction: mass/volume → density, mapping: Fourier/wavelet). PCA: the steps (normalize, find orthonormal directions of maximum variance, keep the top k) and when it helps.
- **Priority:** P2.
- **Skip:** The PCA derivation or proof. Know the procedure and the intuition, not the eigen-algebra.

### T10 · Clustering basics and taxonomy (D4)

- **Read:** D4 slides 2–25.
- **Exam wants:**
  - Clustering vs classification (unsupervised vs supervised)
  - The dimensions of clustering *methods*: hard vs soft, flat vs hierarchical, agglomerative vs divisive, distance- vs density-based, sequential vs simultaneous, monothetic vs polythetic
  - The types of *clusters*: well-separated, center-based, contiguous, density-based, conceptual
  - What makes a good clustering (high intra-cluster, low inter-cluster similarity) and the requirements list on slide 24

  Expect a "match the cluster type to the picture" or "define with example" question.
- **Priority:** P2.
- **Skip:** D4 slides 5–10 (application examples).

### T11 · K-means (D4)

- **Read:** D4 slides 26–52.
- **Exam wants:** The algorithm, **SSE** = Σᵢ Σ_{x∈Cᵢ} dist(μᵢ, x)² (and why the mean minimises it), complexity O(I·K·N·D), and the stopping conditions. **Hand-run 2–3 iterations** on a small 1-D or 2-D set and report SSE. Initial-centroid sensitivity and its fixes (multiple runs, hierarchical seeding, pick more than k and take the most separated, post-processing). Empty clusters (reseed from the highest-SSE cluster), pre/post-processing (split loose clusters, merge close ones), the limitations (differing sizes, densities, non-globular shapes, outliers) with the fix of over-clustering then merging, and choosing K by the elbow.
- **Priority:** P1. This is the top numerical in the whole syllabus.
- **Skip:** D4 slides 38–41 (repeated 10-cluster iteration pictures; the point is made once on 35–37).

### T12 · Hierarchical clustering: agglomerative, divisive, bisecting k-means (D4)

- **Read:** D4 slides 53–88.
- **Exam wants:** **Given a distance matrix, draw the dendrogram under MIN (single), MAX (complete) and group average.** His sample-data examples on slides 74–81 are exactly this. Also:
  - Ward's method = increase in SSE on merging; it's the hierarchical analogue of k-means
  - Which linkage handles non-elliptical shapes (MIN) vs noise (MAX, Ward); the comparison is on slide 83
  - Complexity: O(N²) space, O(N³) time, reducible to O(N² log N)
  - Merges can never be undone
  - Bisecting k-means (split the highest-SSE cluster)
  - Divisive via MST: build the MST, cut the largest edges

  *Cross-course:* single-link = Kruskal stopped at k components (KT 4.7), and MST-divisive = removing the k−1 heaviest MST edges. It's the same algorithm as AAC's MST lecture.
- **Priority:** P1.
- **Skip:** Nothing. Do every sample-data example once by hand.

### T13 · DBSCAN (D4)

- **Read:** D4 slides 89–99 for the algorithm, the point-type pictures, failure modes and the k-dist plot. For the formal reachability definitions, read **Han–Kamber–Pei 3e §10.4.1**: his slide 93 ("How DBSCAN works – formally") is a figure with no text, and his slide 89 uses Han's terms.
- **Exam wants:** Label points **core / border / noise** for a given Eps and MinPts. State whether the point counts itself: his slide says "*more than* MinPts", so say your convention in the answer. Directly density-reachable (asymmetric) → density-reachable → density-connected (symmetric), and a cluster is a maximal density-connected set. Where it fails: varying densities and high-dimensional data. Choosing Eps from the elbow of the sorted k-distance plot, with MinPts = k.
- **Priority:** P1.
- **Skip:** OPTICS and DENCLUE, which sit next to it in Han §10.4.

### T14 · BIRCH (D4)

- **Read:** D4 slides 100–110.
- **Exam wants:**
  - **CF = (N, LS, SS)** and **additivity** CF₁ + CF₂ = (N₁+N₂, LS₁+LS₂, SS₁+SS₂). That additivity is why BIRCH needs only one or two scans.
  - Centroid, radius and diameter computed from the CF alone (a numerical is very likely: points → CF → merge → radius)
  - CF-tree parameters: branching factor B and threshold T. Insertion goes to the closest leaf, then the leaf splits and splits propagate up (his branching-factor-3 example, slides 107–108)
  - The two phases: build the tree, then cluster the leaves
  - Weaknesses: order sensitivity, spherical bias, "unnatural" clusters from the fixed leaf size
- **Priority:** P1.
- **Skip:** None.

### T15 · CURE (D4)

- **Read:** D4 slides 111–114.
- **Exam wants:** Representative points instead of a single centroid. Pass 1: sample, cluster, pick c well-scattered points per cluster, shrink them a fraction α (e.g. 30%) toward the centroid. Pass 2: assign every point to the cluster with the nearest representative. Why shrinking resists outliers and why multiple representatives capture arbitrary shapes. Compare with k-means (one centroid) and MIN (all points).
- **Priority:** P2.
- **Skip:** None.

---

## 2. Study order to the midsem

Phase C (submission Sun 27 Sep, presentation Tue 29 Sep) and three other midsems (05, 07, 08 Oct) compete for the same days. ADM is the lightest to cram and the most numerical, so it gets short daily blocks rather than full days.

| When | ADM work | Why this order |
|---|---|---|
| Thu 24 – Fri 25 Sep | T11 k-means, T12 hierarchical. Hand-run every slide example | Freshest lectures and the heaviest numericals. Clustering is also where a surprise quiz is most likely next |
| Sat 26 – Tue 29 Sep | 30 min/day: T13 DBSCAN, T14 BIRCH, T15 CURE | Phase C week, so keep ADM exam prep light |
| Wed 30 Sep – Thu 01 Oct | T4, T5, T7, T8 (quality, binning, normalization, sampling) | All formula-driven. Make one A5 formula card |
| Fri 02 Oct | T2, T3, T6, T9, T10 (definitions and taxonomies), plus PCA from Han §3.4.3 | Low-effort recall topics |
| Sat 03 – Sun 04 Oct | **ACN has priority.** ADM: 20 min on the formula card only | ACN is on 05 Oct |
| After 05 Oct | Depends on the ADM date. Do one full closed-book pass: redo every numerical cold, then T1 skim | AAC (07) and AOS (08) come first if ADM is later |
| Day before ADM | Formula card + the "fails when" list (k-means, DBSCAN, BIRCH, MIN/MAX) | Those are the (c)-type questions |

**Formula card (closed book, so memorize these):** binning (3 smoothers) · min-max · z-score + MAD variant · decimal scaling j · SSE · k-means O(IKND) · agglomerative O(N²) space / O(N³) time · Ward = ΔSSE · CF additivity · BIRCH centroid LS/N, radius √(SS/N − (LS/N)²) · DBSCAN core/border/noise rule · CURE shrink p′ = p + α(centroid − p).

---

## 3. Skip entirely

- **D1:** slides 2–3, 5–6, 11–13, 23–29, 32–34 (history, alternative names, application galleries).
- **D3:** wavelets, data-cube pictures, the file-formats bullet. Nothing in the decks covers them.
- **D4:** slides 5–10 and 38–41 (repeated application and iteration pictures).
- **Not taught (so far):** classification (Module 2), association rules and Apriori/FP-growth (Module 4), similarity, min-hashing and LSH (Module 5), web search and PageRank (Module 6), cluster evaluation, EDA statistics.
- **Books beyond the two named sections:** Tan–Steinbach–Kumar and MMDS end to end, Kotu & Deshpande (R1 on D4). The slides already condense them. The only book reading is Han 3e §3.4.3 (PCA) and §10.4.1 (DBSCAN definitions).
- **Your project literature** (PlantVillage, CNNs, ViT, XAI). It carries no midsem marks.
