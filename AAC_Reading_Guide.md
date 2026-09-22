# CS G526 Advanced Algorithms and Complexity: Reading Guide and Exam Weights

**Source:** course page (last updated 18/09, through Lec 16) + handout (updated 14/08). Instructor: Aniket Basu Roy.
**Primary text:** KT = Kleinberg & Tardos, *Algorithm Design*. Every lecture so far maps to a KT section.
**Secondary:** DPV = Dasgupta, Papadimitriou, Vazirani (shorter, cleaner, good second explanation) · Vaz = Vazirani, *Approximation Algorithms* · MR = Motwani & Raghavan, *Randomized Algorithms* · Erickson = Jeff Erickson, *Algorithms* (free PDF, jeffe.cs.illinois.edu/teaching/algorithms; excellent on flows and reductions, not on the handout).

---

## 0. Read this first

**Exams**
- **Midsem: Wed 07 Oct, 9:30–11:00, 90 min, OPEN book, 25%.** That's the day *before* the AOS midsem (08 Oct). Plan around that: G526 prep must be done by the weekend of 3–4 Oct, and the evening of 7 Oct goes to AOS.
- **Comprehensive: Fri 04 Dec, 10:00–1:00, 180 min, CLOSED book, 40%.** Cumulative.

**What open book means here.** Nobody sets "state Dijkstra" in an open-book exam. Expect *new* problems in the KT-exercise style: "design an algorithm, prove it correct, analyse the running time." The book won't save you; practice will. Reading is maybe 30% of the prep, solving problems is 70%.

**What his course actually tests** (handout objective 1 and 5): formalize the problem, design, **prove correctness**, analyse. Proofs carry the marks. A correct algorithm with a hand-wavy proof gets partial credit at best.

**Guesstimated midsem syllabus:** Lec 1 through whatever flow lectures happen before 07 Oct. Remaining slots: 23, 25, 28, 30 Sep and 5 Oct (2 Oct is Gandhi Jayanti). So expect flows through max-flow/min-cut and probably bipartite matching and a couple of applications. LP probably comes after the midsem.

**Weights below are my guesses**, based on lecture time spent, the handout's emphasis, and how algorithms courses in the KT mould are usually examined. Treat them as triage, not truth.

---

## 1. Midsem weight map (guesstimate)

| Block | Lectures | Est. share of midsem | Why |
|---|---|---|---|
| Stable matching + asymptotics/graphs | 1–2 | ~5% | Warm-up. Maybe a short proof or growth-rate ordering. |
| Greedy | 3–7 | ~25% | 5 lectures. Exchange-argument proofs are exam staples. |
| Divide & conquer | 8–9 | ~15% | Recurrences + one design problem. The rectangle paper is his own addition, so it's fair game. |
| Dynamic programming | 10–15 | **~35%** | 6 lectures, most time spent. Near-certain "design a DP" question. |
| Network flow | 16 → ~21 | ~20% | Most recent. Likely a "reduce this to max-flow" question. |

**Priority order for your time:** DP > Greedy proofs > Flow reductions > D&C > warm-up.

---

## 2. Chapter by chapter

Each block: **Topics** (what he covered) · **Read** · **Do** (practice) · **Exam angle** (what questions will look like).

### Block A: Warm-up (Lec 1–2) · midsem ~5% · comp ~2%

**Topics:** Gale–Shapley stable matching; proposer-optimality; O(n²) implementation. Big-O/Ω/Θ, growth-rate ordering, graph basics (BFS/DFS, connectivity, bipartiteness, DAGs/topological order).

**Read**
- KT 1.1 (stable matching), 2.1–2.4 (asymptotics, common running times), 3.1–3.6 (graphs). Skim 3 if your UG graph basics are solid.
- **His scribes for Lec 1 and Lec 2** (linked on the course page). These are the best record of what *he* emphasized.

**Do:** KT Ch 1 Solved Exercises; KT Ch 2 exercises on ordering functions by growth rate (a few).

**Exam angle:** "Prove Gale–Shapley terminates in ≤ n² proposals" / "Is there always a stable matching where X?" / order these functions. Cheap marks; don't over-invest.

---

### Block B: Greedy (Lec 3–7) · midsem ~25% · comp ~10%

**Topics**
- Lec 3: interval scheduling (earliest finish time), "greedy stays ahead" proof. KT 4.1 also has interval partitioning (depth = lower bound).
- Lec 4: MST via Prim–Jarník, **cut property**. HW: prove O(m log n) with a min-heap.
- Lec 5: Dijkstra. HW: same heap analysis.
- Lec 6–7: Huffman codes: running time, then **correctness proof** (exchange argument; optimal tree is full; lowest-frequency letters are siblings at the deepest level).

**Read**
- **KT 4.1, 4.2** (4.2 = scheduling to minimize lateness, the cleanest **exchange argument** in the book; read it even if not lectured).
- **KT 4.4** (Dijkstra), **4.5** (MST, cut + cycle property, Kruskal too), 4.6 (union-find, for Kruskal's running time), **4.8** (Huffman).
- DPV 5.1 (MST), 5.2 (Huffman) as a second pass. DPV 4.4 for Dijkstra.

**Do**
- The two non-eval HWs (heap-based running-time proofs). Do them properly, they're exactly midsem-sized.
- KT Ch 4 Solved Exercises (both).
- 4–5 KT Ch 4 end-of-chapter exercises. Prioritize ones that ask "prove or give a counterexample" and ones about MST properties (unique MST when weights are distinct, MST under changed weights, etc.).

**Exam angle**
- "Here's a greedy rule. Prove it optimal or give a counterexample." Learn to kill a bad greedy rule with a 3-element counterexample fast.
- Proof templates to have indexed on your open-book sheet: *greedy stays ahead*, *exchange argument*, *cut property*.
- Heap running-time analysis.

---

### Block C: Divide and conquer (Lec 8–9) · midsem ~15% · comp ~5%

**Topics**
- Lec 8: closest pair of points in the plane, O(n log n); the strip argument (only ~7 neighbours to check).
- Lec 9: **maximum independent set of axis-parallel rectangles** (Agarwal, van Kreveld, Suri 1998, §3). Split at the median vertical line; rectangles stabbed by the line reduce to an interval problem in y (solved exactly by greedy interval scheduling); recurse on each side; return the better of (stabbed set) vs (left ∪ right). This gives an **O(log n)-approximation** in O(n log n) time.

**Read**
- KT 5.1–5.2 (recurrences, unrolling), 5.3 (counting inversions), **5.4** (closest pair). KT 5.5 (integer multiplication) is good recurrence practice.
- DPV 2.2 (**master theorem**, cleaner than KT's version).
- **The paper, §3 only** (doi.org/10.1016/S0925-7721(98)00028-5). It's short. Note this is secretly an *approximation algorithm*: a preview of post-midsem material, and he's the kind of instructor who likes connecting them in the comprehensive.

**Do**
- KT Ch 5 Solved Exercises.
- KT Ch 5 Ex 1 (median of two sorted databases) and Ex 2 (significant inversions). Both are classic midsem-sized D&C.
- Re-derive the log n approximation ratio for the rectangle algorithm yourself: why is the recursion depth log n, and why does that give the ratio?

**Exam angle:** solve a recurrence; modify closest pair/inversions; "prove the approximation ratio / running time of this D&C." Master theorem goes on the open-book sheet.

---

### Block D: Dynamic programming (Lec 10–15) · midsem **~35%** · comp ~12%

**Topics**
- Lec 10: weighted interval scheduling (memoization vs iteration, solution reconstruction).
- Lec 11: subset sum / knapsack. HW: **pseudo-polynomial time**. Know why O(nW) isn't polynomial (W is exponential in its bit length). This comes back in NP-completeness.
- Lec 12–14: sequence alignment (edit distance), then **Hirschberg's linear-space version** (KT 6.7: DP + D&C). HW: alignment as a shortest path in a grid graph.
- Lec 15: **Bellman–Ford** (KT 6.8) and **negative cycle detection** (KT 6.10).

**Read**
- **KT 6.1, 6.2** (principles of DP), 6.3 (segmented least squares: a "multi-way choice" DP), **6.4, 6.6, 6.7, 6.8, 6.10**.
- **KT 6.9 (distance vector protocols)** isn't lectured, but it's G525 routing material. One read covers both courses.
- DPV Ch 6 is the best second pass: short, many examples (LIS, edit distance, knapsack, chain matrix multiplication, TSP DP).

**Do** (the highest-return practice in this course)
- KT Ch 6 Solved Exercises.
- KT Ch 6 Ex 1 (max-weight independent set on a path; shows why greedy fails), Ex 2 (high-stress/low-stress jobs). Then 4–6 more from Ch 6.
- DPV Ch 6 exercises: pick 5 across different "shapes": prefix DP, interval DP, subset/knapsack-style, DP on DAGs/trees.
- For every problem, write the four parts explicitly: **(1) subproblem definition in words, (2) recurrence with base cases, (3) evaluation order + running time, (4) correctness via induction on the recurrence.** That structure is what gets graded.

**Exam angle:** "Design an O(n²) algorithm for X" where X is a disguised DP. Also: reconstruct the solution; argue pseudo-polynomial vs polynomial; space reduction à la Hirschberg; negative cycle detection with Bellman–Ford.

**Cross-course:** Bellman–Ford = distance vector (G525). Dijkstra = link state (G525). The Go shortest-path implementation from your project plan covers both.

---

### Block E: Network flow (Lec 16 → ~Lec 21) · midsem ~20% · comp ~10%

**Topics (lectured + expected)**
- Lec 16: max-flow problem, Ford–Fulkerson, residual graphs (KT 7.1).
- Expected before midsem: **max-flow min-cut theorem** (7.2), maybe capacity scaling (7.3), **bipartite matching** (7.5), disjoint paths (7.6), then possibly applications (7.7 circulations with demands, 7.8 survey design, 7.10 image segmentation, 7.11 project selection, 7.12 baseball elimination).

**Read**
- **KT 7.1, 7.2, 7.5** are core. 7.3 (scaling) for running-time questions. 7.6–7.12: read the ones he lectures, and skim the rest for reduction patterns.
- Erickson Ch 10–11 (max-flow/min-cut, applications) is an excellent alternate explanation.
- DPV 7.2 for a compact view of flow as an LP (this bridges to the post-midsem LP block).

**Do**
- KT Ch 7 Solved Exercises.
- Practice **reductions to flow**: build the graph, say what capacities mean, then prove *both directions* (a flow of value k gives a solution of size k, and vice versa). Missing the reverse direction is the most common lost mark.
- Min-cut ↔ certificate arguments (why the cut proves optimality).

**Exam angle:** "Model this as max-flow/min-cut and prove correctness." Integrality theorem usage. Running time of FF (O(mC)) vs scaling.

---

## 3. Post-midsem (comprehensive only)

These haven't started yet. Weights are for the **comprehensive (40%, closed book)**, which I expect to split roughly **40% pre-midsem / 60% post-midsem**. Closed book means you need the definitions, key theorems and standard reductions *memorized*, not just indexed.

| Topic | Est. comp share | Read | Notes |
|---|---|---|---|
| **Linear programming + duality** | ~10% | DPV Ch 7 (best intro); Vaz Ch 12 (LP duality) | Formulating problems as LPs; weak/strong duality; flow as an LP. Feeds LP-rounding approximations. |
| **P, NP, NP-completeness, reductions** | **~20%** | **KT 8.1–8.8, 8.10**; DPV Ch 8; Erickson Ch 12 | The highest-value post-midsem block. Master the canonical chain: SAT → 3-SAT → Independent Set → Vertex Cover → Set Cover; 3-SAT → Hamiltonian Cycle → TSP; 3-Coloring; Subset Sum. Expect "prove X is NP-complete." |
| PSPACE | ~3% | KT 9.1–9.3 | Definitions and one example (QSAT, games). Low priority. |
| **Approximation algorithms** | ~15% | **KT 11.1–11.4, 11.6, 11.8**; Vaz Ch 1–2 (vertex cover, set cover), Ch 14 (LP rounding) | Load balancing (2-approx), center selection, set cover (H(n)), vertex cover via pricing and via LP rounding, knapsack FPTAS. Proving the ratio is the whole game. |
| **Randomized algorithms** | ~10% | **KT 13.1–13.5, 13.9, 13.12**; MR Ch 1 | Karger's min-cut, randomized quicksort/median, MAX-3-SAT (7/8), linearity of expectation, Chernoff (statement). Also Las Vegas vs Monte Carlo. |
| Local search | ~2% | KT 12.1–12.4 | On the website list, not in the handout. Skim unless lectured. |
| Hardness of approximation | ~0–2% | Vaz Ch 29 (intro only) | On the website, not the handout. Likely one lecture at most; read only if he covers it. |

**If he assigns paper reading in approximation or randomized algorithms**, lean toward distributed/networked ones (e.g., randomized load balancing, consistent hashing, distributed MIS/Luby's algorithm). They double as track reading.

---

## 4. Open-book midsem sheet: what to put on it

One indexed sheet (or a few pages) that saves you *time* in the exam. It shouldn't be a copy of the book.

1. **Proof templates:** greedy stays ahead; exchange argument; cut/cycle property; induction for DP correctness; flow reduction (both directions); min-cut certificate.
2. **Recurrences:** master theorem (DPV form) + the common ones (T(n)=2T(n/2)+n, T(n)=T(n/2)+1, T(n)=2T(n/2)+1, T(n)=aT(n/b)+n^d).
3. **Running times:** heap ops, Dijkstra/Prim O(m log n), Kruskal with union-find, FF O(mC), scaling O(m² log C), Bellman–Ford O(mn), alignment O(mn) time / O(m+n) space (Hirschberg).
4. **DP catalogue:** one line per lectured problem: subproblem, recurrence, running time.
5. **Counterexample bank** for the classic wrong greedy rules (shortest interval first, fewest conflicts first, earliest start first, etc.).
6. **Page index into KT** for each lectured section, so you can find a figure in 10 seconds.

---

## 5. Plan until 07 Oct (with AOS on 08 Oct)

| Days | G526 | Notes |
|---|---|---|
| 22–25 Sep | DP block: read KT 6.1–6.10, do Solved Exercises + 4 problems | DP is the biggest share; start there. |
| 26–28 Sep | Greedy: KT 4.1/4.2/4.5/4.8 + non-eval HWs + 4 problems | Weekend: heavier session. |
| 29 Sep–1 Oct | D&C (KT 5.1–5.4, paper §3) + flow as lectured (7.1, 7.2, 7.5) | Keep up with flow in real time; it's the freshest material. |
| 2–4 Oct | Mixed timed practice: 3 problems in 90 min, twice. Build the open-book sheet. | 2 Oct is a holiday: use it. |
| 5–6 Oct | Final flow lectures + reductions practice; finish the sheet | Light touch on AOS in parallel. |
| 7 Oct | Exam morning. **Evening = AOS only.** | |

**Continuous evaluation, don't forget:** problem sets (attempt *every* problem, two are graded after submission, and oral checks can hit any of them) and scribing (10%, one lecture: volunteer for a flow or NP-completeness lecture, where a clean scribe is also great exam revision).
