# CS G526 Advanced Algorithms and Complexity: Midsem Guide

Scope is **Lectures 1–18**, everything on the course page up to 23 Sep, plus PS1. Every topic has one source. Unless marked otherwise that source is **KT** (Kleinberg & Tardos, *Algorithm Design*), and the page numbers are the **printed book pages**. In `kt.pdf`, add about 25 to get the PDF page (about 26 in Ch 7). The `(4.17)`-style tags are KT's numbered statements, which are the fastest way to find anything in an open-book exam.

---

## 0. The exam

| | |
|---|---|
| **Date** | **Wed 07 Oct 2026, 9:30–11:00** (90 min). The day before AOS (08 Oct), two days after ACN (05 Oct) |
| **Format** | **Open book**, 25%. No AI and no external help |
| **Syllabus** | Not announced. Assume everything lectured up to 05 Oct. This guide covers Lec 1–18. Lectures on 25, 28 and 30 Sep and 5 Oct (flows, probably KT 7.3/7.5 onward) are **not** included yet. Add them after they're taught |
| **Style signal** | PS1 is his only exam-style artifact so far: formal c, n₀ proofs, "prove optimal via exchange argument", "prove or give a counterexample", generalize an algorithm (ternary Huffman). Expect the midsem to look like PS1 with less time |

**What open book changes.** You don't get marks for reproducing KT. You get them for a *new* problem's algorithm, correctness proof and running time. The book is only useful if you can find a lemma in 10 seconds, and that's what the reference sheet in §3 is for.

**Priority key:** **P1** = near-certain to be examined, full depth · **P2** = likely a sub-part, know it cold but spend less time · **P3** = warm-up, skim.

---

## 1. Topics, in lecture order

### T1 · Stable matching (Lec 1)

- **Read:** KT 1.1, pp. 1–12.
- **Exam wants:** Termination in ≤ n² proposals, output is a perfect stable matching, and proposer-optimal/receiver-pessimal (PS1 Q2). If asked, it's one short proof by contradiction.
- **Priority:** P3. You already proved the hard part for PS1.
- **Skip:** KT 1.2 (five representative problems), KT 2.3 (GS with lists and arrays).

### T2 · Asymptotic notation (Lec 2)

- **Read:** **CLRS 3e §3.1, pp. 43–53.** This is the one exception to KT: KT 2.2 has no little-o, and PS1 Q1 used it.
- **Exam wants:** Prove or disprove a composition claim (O·o, Θ transitivity, sums, logs vs polys) with explicit c and n₀. It's cheap marks if the quantifiers are right.
- **Priority:** P2.
- **Skip:** CLRS §3.2 (standard functions) and KT 2.1–2.2.

### T3 · Graph review (Lec 2)

- **Read:** KT 3.4 (bipartiteness, pp. 94–97) and 3.6 (DAGs and topological order, pp. 99–104) only.
- **Exam wants:** Not examined on its own. BFS layers, bipartiteness and topological order show up as *subroutines* inside flow and DP answers.
- **Priority:** P3.
- **Skip:** KT 3.1–3.3 and 3.5 (UG material).

### T4 · Interval scheduling: greedy stays ahead (Lec 3)

- **Read:** KT 4.1, pp. 116–125 (includes interval partitioning).
- **Exam wants:** A "greedy stays ahead" induction (4.2)→(4.3), plus a 3-interval counterexample for each wrong rule (shortest first, fewest conflicts, earliest start). Interval partitioning: depth is a lower bound (4.4) and the greedy achieves it.
- **Priority:** P1. This is also the subroutine inside T11.
- **Skip:** Nothing in 4.1.

### T5 · Scheduling to minimize lateness: exchange argument (PS1 Q3)

- **Read:** KT 4.2, pp. 125–131.
- **Exam wants:** Earliest-deadline-first plus the exchange argument: no idle time, all inversion-free schedules tie (4.8), and swapping an *adjacent* inversion doesn't increase max lateness (4.9)→(4.10). This is the canonical exchange template.
- **Priority:** P1.
- **Skip:** KT 4.3 (optimal caching).

### T6 · Dijkstra (Lec 5)

- **Read:** KT 4.4, pp. 137–142.
- **Exam wants:** The correctness invariant (4.14) proved by induction on |S|, and O(m log n) with a heap (4.15). Know why it breaks with negative edges. PS1 Q4 showed that reweighting by +|w_min|+1 fails because it penalises paths with more edges.
- **Priority:** P1.
- **Skip:** The Ch 2.5 heap internals. Just know the op costs.

### T7 · Minimum spanning trees (Lec 4)

- **Read:** KT 4.5, pp. 142–151.
- **Exam wants:** The cut property (4.17) and cycle property (4.20) with exchange proofs, Prim/Kruskal correctness from them, and O(m log n). Typical variants: MST is unique when weights are distinct, MST after one edge weight changes, the "is edge e in some or every MST" test.
- **Priority:** P1.
- **Skip:** KT 4.6 (union-find internals; just quote Kruskal = O(m log n)), 4.7 (clustering), 4.9 (arborescences).

### T8 · Huffman codes (Lec 6–7)

- **Read:** KT 4.8, pp. 161–177.
- **Exam wants:** The optimality proof chain: the optimal tree is full (4.28), the two lowest-frequency letters are deepest siblings (4.31), then induction on alphabet size (4.33). Running time is O(k log k). Expect a generalization twist like ternary (PS1 Q5): pad with zero-frequency dummies until (n − 1) is divisible by 2.
- **Priority:** P1.
- **Skip:** The "extensions" subsection at the end of 4.8 (arithmetic coding, adaptive).

### T9 · Recurrences (Lec 8–9 tooling)

- **Read:** **DPV §2.2, pp. 49–51** (the master theorem).
- **Exam wants:** Solve T(n) = aT(n/b) + O(n^d) in one line, and unroll anything the theorem doesn't fit (e.g. 2T(n/2) + n log n) with a recursion tree.
- **Priority:** P2. It sits inside every D&C answer.
- **Skip:** KT 5.1–5.2. They teach the same thing more slowly.

### T10 · Closest pair of points (Lec 8)

- **Read:** KT 5.4, pp. 225–231.
- **Exam wants:** Presort by x and y, split at the median, and the δ-strip packing argument: each point checks ≤ 15 neighbours in S_y (5.10). Total is O(n log n) (5.11). Likely twists: a different dimension or metric, or "why is the constant bounded".
- **Priority:** P2.
- **Skip:** KT 5.3 (counting inversions), 5.5 (integer multiplication), 5.6 (FFT). None were lectured.

### T11 · Max independent set of rectangles, D&C approximation (Lec 9)

- **Read:** **Agarwal, van Kreveld & Suri (1998), *Label placement by maximum independent set in rectangles*, Comput. Geom. 11:209–218, §3 only.**
- **Exam wants:** The algorithm: vertical median line, rectangles it stabs reduce to interval scheduling in y (exact, T4), recurse left/right, return the larger of the two. Then the proof: recursion depth is log n and OPT splits across levels, so the ratio is O(log n) and the time is O(n log n). This is his own addition, so it's very likely to appear.
- **Priority:** P1.
- **Skip:** The rest of the paper (the equal-height-rectangle results).

### T12 · Weighted interval scheduling (Lec 10)

- **Read:** KT 6.1–6.2, pp. 252–261.
- **Exam wants:** The DP template in four parts: subproblem in words, recurrence OPT(j) = max(v_j + OPT(p(j)), OPT(j−1)) (6.1), order and running time (O(n log n) with the sort), and correctness by induction (6.3). Also traceback to recover the set.
- **Priority:** P1. Every DP answer you write should follow this shape.
- **Skip:** KT 6.3 (segmented least squares, not lectured).

### T13 · Subset sum and knapsack (Lec 11)

- **Read:** KT 6.4, pp. 266–272.
- **Exam wants:** The 2-D subproblem OPT(i, w) (6.8), O(nW) (6.9), and **why O(nW) is pseudo-polynomial** (W takes log W bits). Twists: exact-sum feasibility, bounded copies, minimising instead of maximising.
- **Priority:** P1.
- **Skip:** KT 6.5 (RNA secondary structure).

### T14 · Sequence alignment and Hirschberg (Lec 12–14)

- **Read:** KT 6.6–6.7, pp. 278–290.
- **Exam wants:** The alignment recurrence with gap δ and mismatch α (6.16) and the grid-graph shortest-path view. Linear space: f(i, j) + g(i, j) is the best path through (i, j) (6.19), splitting at column n/2 (6.20), giving O(mn) time and O(m + n) space. He spent three lectures here, so expect a full question.
- **Priority:** P1.
- **Skip:** Nothing. Read both sections completely.

### T15 · Bellman–Ford and negative cycles (Lec 15)

- **Read:** KT 6.8, pp. 290–297 and KT 6.10, pp. 301–307.
- **Exam wants:** The recurrence OPT(i, v) over "≤ i edges" (6.23), O(mn) (6.25), the O(n)-space version and pointer graph (6.26–6.27), and negative-cycle detection: OPT(n, v) ≠ OPT(n−1, v) for some v (6.30). This links to PS1 Q4: this is the *correct* way to handle negative edges.
- **Priority:** P1.
- **Skip:** KT 6.9 (distance vector). G525 doesn't teach routing, so it has no cross-course payoff.

### T16 · Max flow, residual graphs, Ford–Fulkerson (Lec 16–17)

- **Read:** KT 7.1, pp. 338–346.
- **Exam wants:** Build G_f (forward and backward edges), augment along a path, and show flow stays integral (7.2). The O(mC) bound (7.5) and why it's not polynomial in input size. You may be asked to run 2–3 augmentations by hand.
- **Priority:** P1.
- **Skip:** Nothing in 7.1.

### T17 · Max-flow min-cut theorem (Lec 18)

- **Read:** KT 7.2, pp. 346–352.
- **Exam wants:** ν(f) = f_out(A) − f_in(A) ≤ c(A, B) for any cut (7.6–7.8). No s–t path in G_f means the set A* reachable from s in G_f is a min cut (7.9), which gives max-flow = min-cut (7.13) and integrality (7.14). Expect "find a min cut from this residual graph", or a reduction where the cut is the certificate.
- **Priority:** P1.
- **Skip:** KT 7.3+ until he lectures them.

---

## 2. Study order to 07 Oct

The plan is built around ACN (05 Oct), AOS (08 Oct) and the ADM Phase C deadline (27 Sep, presentation 29 Sep). Newest material goes first, because it's the least rehearsed.

| Day | AAC work | Around it |
|---|---|---|
| Thu 24 – Fri 25 Sep | T16, T17 (flow). Redo Lec 16–18 examples by hand | Keep pace with Fri's lecture |
| Sat 26 – Sun 27 Sep | T14, T15 (alignment, Hirschberg, Bellman–Ford) | ADM Phase C due Sun 11:30 pm |
| Mon 28 – Tue 29 Sep | T12, T13 (WIS, knapsack), then T4, T5 (proof templates) | ADM presentation Tue |
| Wed 30 Sep | T6, T7, T8 (Dijkstra, MST, Huffman) | |
| Thu 01 Oct | T9, T10, T11 (D&C + rectangles). T1–T3 quick skim. **Build reference sheet v1** | |
| Fri 02 Oct (holiday) | **Timed mock:** 3 KT end-of-chapter problems (Ch 4, 6, 7) in 90 min, open book, using only your sheet | Heaviest AAC day |
| Sat 03 – Sun 04 Oct | AAC off except 30 min adding new flow lectures to the sheet | **ACN first** |
| Mon 05 Oct | ACN exam. Evening: integrate the 05 Oct lecture into the sheet | |
| Tue 06 Oct | Second timed mock, then finalise and print the sheet | |
| Wed 07 Oct | **Exam 9:30.** Evening is AOS only | |

---

## 3. One-page indexed reference sheet (open book)

Print one A4 sheet, both sides, in the layout below. Sections A–E go on the front and F on the back. The point is **lookup speed**: every line ends in a KT statement number or page, so the book opens straight to the proof.

```text
┌──────────────────────────── FRONT ────────────────────────────┐
│ A. PROOF TEMPLATES (write the skeleton, not the prose)        │
│  A1 Greedy stays ahead ..... induction on r  (4.2)→(4.3) p.121│
│  A2 Exchange argument ...... adjacent inversion swap          │
│                              (4.8)-(4.10) pp.129–131          │
│  A3 Cut / cycle property ... (4.17) p.145 · (4.20) p.148      │
│  A4 Huffman chain .......... full (4.28) → siblings (4.31)    │
│                              → induction (4.33) pp.168–175    │
│  A5 DP correctness ......... strong induction on recurrence   │
│                              (6.3) p.255                      │
│  A6 Flow ≤ cut; min-cut .... (7.6)-(7.9), (7.13) pp.347–351   │
│  A7 Reduction to flow ...... build G · flow→solution ·        │
│                              solution→flow · integrality(7.14)│
├───────────────────────────────────────────────────────────────┤
│ B. RECURRENCES                                                │
│  Master (DPV 2.2 p.49): T=aT(n/b)+O(n^d)                      │
│  d > log_b a → n^d  ·  d = log_b a → n^d log n                │
│  d < log_b a → n^(log_b a)                                    │
│  2T(n/2)+n = n log n · T(n/2)+1 = log n · 2T(n/2)+1 = n       │
│  2T(n/2)+n log n = n log² n (tree; master doesn't apply)      │
├───────────────────────────────────────────────────────────────┤
│ C. RUNNING TIMES                                              │
│  Heap op O(log n) · Dijkstra/Prim O(m log n) (4.15)           │
│  Kruskal O(m log n) · Huffman O(k log k) · Closest pair       │
│  O(n log n) (5.11) · WIS O(n log n) · Knapsack O(nW)=pseudo-  │
│  poly (6.9) · Alignment O(mn) / Hirschberg O(m+n) space ·     │
│  Bellman–Ford O(mn) (6.25) · FF O(mC) (7.5)                   │
├───────────────────────────────────────────────────────────────┤
│ D. DP CATALOGUE (subproblem | recurrence | time | KT)         │
│  WIS      | OPT(j)  | max(v_j+OPT(p(j)),OPT(j−1)) | 6.1 p.254 │
│  Knapsack | OPT(i,w)| max(OPT(i−1,w), w_i+OPT(i−1,w−w_i))     │
│           |         |                          | 6.8 p.269    │
│  Align    | OPT(i,j)| min(α+OPT(i−1,j−1), δ+OPT(i−1,j),       │
│           |         |     δ+OPT(i,j−1))       | 6.16 p.282    │
│  B–F      | OPT(i,v)| min(OPT(i−1,v), min_w OPT(i−1,w)+c_vw)  │
│           |         |                         | 6.23 p.294    │
├───────────────────────────────────────────────────────────────┤
│ E. COUNTEREXAMPLE BANK (draw each one tiny)                   │
│  Interval sched: shortest-first · fewest-conflicts ·          │
│   earliest-start (all fail)                                   │
│  Lateness: shortest-job-first · smallest slack d−t (fail)     │
│  Dijkstra with negative edge · "+|w_min|+1" reweighting (PS1) │
│  Greedy knapsack by ratio (fails for 0/1)                     │
└───────────────────────────────────────────────────────────────┘
┌──────────────────────────── BACK ─────────────────────────────┐
│ F. PAGE INDEX (KT printed page · PDF ≈ +25)                   │
│  1.1 SM p.1 · 3.4 bipartite p.94 · 3.6 DAG p.99               │
│  4.1 p.116 · 4.2 p.125 · 4.4 p.137 · 4.5 p.142 · 4.8 p.161    │
│  5.4 p.225 · DPV 2.2 p.49 · CLRS 3.1 p.43 · AvKS'98 §3        │
│  6.1 p.252 · 6.4 p.266 · 6.6 p.278 · 6.7 p.284 · 6.8 p.290    │
│  6.10 p.301 · 7.1 p.338 · 7.2 p.346                           │
│  [space: lectures 25/28/30 Sep + 5 Oct → 7.3 p.352, 7.5 p.367]│
│ G. EXAM ROUTINE (90 min)                                      │
│  Read all · rank by marks · per Q: model → algorithm →        │
│  proof (template A#) → time (C) · 5 min reserve               │
└───────────────────────────────────────────────────────────────┘
```

---

## 4. Skip entirely

These are outside the midsem or already covered by the one source above. Don't open them before 07 Oct.

- **KT:** 1.2 · 2.1, 2.3, 2.4, 2.5 · 3.1–3.3, 3.5 · 4.3 optimal caching · 4.6 union-find · 4.7 clustering · 4.9 arborescences · 5.1–5.3, 5.5, 5.6 · 6.3 segmented least squares · 6.5 RNA · 6.9 distance vector · 7.3 onward (until lectured) · every "Notes and Further Reading".
- **Parallel books:** the DPV, Erickson, CLRS, Brassard & Bratley, Motwani & Raghavan and Vazirani versions of the topics above. The only exceptions are DPV 2.2 (T9) and CLRS 3.1 (T2).
- **Post-midsem blocks:** LP and duality, NP-completeness, approximation, randomized algorithms.

For practice problems and the full-semester plan, see the [AAC Reading Guide](AAC_Reading_Guide.html).
