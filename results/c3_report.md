# C3 Analysis Report: Skill-Guided Checklist vs Free Q&A

## Executive Summary

The Iron Rule Checklist cuts the miss rate by 80% relative to free Q&A (30 → 6), with zero regressions. False pass rate drops from 82.4% (baseline) to 3.5%.

| Metric              | C1 (Baseline) | C2 (Free Q&A) | C3 (Checklist) | Δ (C2→C3) |
|---------------------|---------------|----------------|-----------------|------------|
| Hit (notice issue)  | —             | 196 (86.7%)    | 220 (97.3%)     | +24        |
| Fix (resolve issue) | —             | 189 (83.6%)    | 218 (96.5%)     | +29        |
| Partial             | —             | 7 (3.1%)       | 2 (0.9%)        | −5         |
| Miss                | —             | 30 (13.3%)     | 6 (2.7%)        | −24        |
| **False pass rate** | **82.4%**     | **16.4%**      | **3.5%**        | **−12.9pp**|

---

## 1. Transition Analysis

### C2 → C3 Transition Matrix (226 SNP cases)

| C2 Status → | C3: Fix | C3: Partial | C3: Miss | Total |
|-------------|---------|-------------|----------|-------|
| C2: Fix     | **189** | 0           | **0**    | 189   |
| C2: Partial | 5       | 2           | 0        | 7     |
| C2: Miss    | **24**  | 0           | 6        | 30    |
| **Total**   | **218** | **2**       | **6**    | **226**|

**Zero regressions from C2-fixed cases.** Every case free Q&A resolved, the checklist also resolves. The checklist is a strict superset of free Q&A's coverage.

### C2 Miss → C3 Hit (24 improvements)

| Case    | Type | What C3 Caught |
|---------|------|----------------|
| adv5-B  | Pre  | LSB-first digit ordering deduced from example |
| adv5-C  | Pre  | Digit-order ambiguity between title ("Reverse Order") and description |
| adv8-C  | T    | Multiple valid starting stations — tiebreaker |
| adv12-C | E    | Explicitly confirms None for failure cases, making implicit sentinel explicit |
| adv17-A | O    | "Must the output be a permutation of the input?" |
| adv18-B | R    | Digit base (base 10 vs other) with worked example |
| adv20-C | R    | "Base of digits" — directly asks whether base-10 is assumed |
| adv27-A | A    | Contiguous vs scattered subsequence |
| adv31-A | D    | Strict vs non-strict increasing |
| adv32-B | E    | Confirms "greater than the last" = strict; invites correction |
| adv33-B | D    | Confirms "getting bigger" = strict |
| adv33-C | D    | "Bigger than" strictness with witness |
| adv34-B | A    | Strict + subsequence vs subarray |
| adv48-A | O    | "Must the output be a permutation of the input?" |
| adv51-C | Pre  | Input sorted precondition |
| adv52-C | D    | Missing elements handling |
| adv63-A | A    | "Unique" = distinct values vs appearing-exactly-once |
| adv65-A | A    | "Flip backwards at what level? Character-by-character vs word-by-word" |
| adv65-B | A    | Character-level vs word-level reversal |
| adv68-C | Pre  | Digit characters in input — addresses precondition gap |
| adv70-B | E    | Crossing interaction when 1 and max pass each other |
| adv74-C | O    | Single-element subarrays (start=end) |
| adv81-A | A    | "Drop duplicates" = keep-one vs remove-all-copies |
| adv81-B | A    | "Unique" = distinct vs appearing-exactly-once |

---

## 2. Per-Issue-Type Comparison

| Type | N  | C2 Hit% | C3 Hit% | C2 Miss | C3 Miss | Δ Miss |
|------|----|---------|---------|---------|---------|--------|
| A    | 53 | 81%     | **94%** | 10      | 3       | **−7** |
| D    | 39 | 90%     | **100%**| 4       | 0       | **−4** |
| E    | 37 | 89%     | **97%** | 4       | 1       | **−3** |
| Pre  | 41 | 90%     | **100%**| 4       | 0       | **−4** |
| O    | 21 | 76%     | **90%** | 5       | 2       | **−3** |
| T    | 17 | 94%     | **100%**| 1       | 0       | **−1** |
| P    | 14 | 100%    | 100%    | 0       | 0       | 0      |
| R    | 4  | 50%     | **100%**| 2       | 0       | **−2** |

Four issue types reach 100% detection: Definition (D), Precondition (Pre), Tiebreaker (T), Representation (R). The checklist's structured categories directly cover these.

---

## 3. Per-Persona Comparison

| Persona | C2 Hit% | C3 Hit% | C2 Miss | C3 Miss | Δ Miss |
|---------|---------|---------|---------|---------|--------|
| A       | 89%     | **98%** | 9       | 2       | −7     |
| B       | 86%     | **96%** | 11      | 3       | −8     |
| C       | 85%     | **99%** | 10      | 1       | −9     |

All three personas improve substantially. Persona C (process-oriented NLs) shows the largest absolute gain, consistent with the checklist being most effective at exposing unstated preconditions embedded in procedural descriptions.

---

## 4. The 6 Remaining Misses

These 6 cases fall into three structurally distinct categories.

### Category 1: Genuine NL ambiguity (2 cases)

**adv9-C** [E]: The NL says "every number from 1 up to n-1." The checklist faithfully reproduces this range without asking whether it should start at 0. VERINA includes 0. For n=5, d=3: starting at 0 gives count=2; starting at 1 gives count=1.

**adv40-A** [A]: The NL says "top value in the list." The checklist only asks about non-empty precondition. "Top" could mean maximum (VERINA's intent), first element, or last element. For [3,1,9,2]: max=9, first=3, last=2.

*Fixable by adding a checklist category for "verify the core operation word."*

### Category 2: Example inadequacy with clear NL (2 cases)

**adv57-B** [A]: The NL says "bigger" which is unambiguously strict (>) in English. But the example uses all distinct values, so it can't distinguish > from ≥. The checklist doesn't explicitly confirm "bigger = strictly greater" the way it does for adv32-B ("greater than the last — confirmed strictly bigger").

**adv63-B** [A]: The NL says "how many different numbers" which clearly means distinct count. But the example [0,0,1,1,1,2,2,3,3,4]→5 happens to equal max−min+1=5. The checklist doesn't generate a distinguishing test case.

*Partially fixable: adding explicit confirmation statements would catch adv57-B. adv63-B's alternative interpretation ("range size") is too unnatural to warrant a standard checklist probe.*

### Category 3: Formal spec completeness gap (2 cases)

**adv41-A** [O] and **adv41-B** [O]: The NL says "biggest of three numbers." No NL ambiguity exists — every human reads this as max(a,b,c). The issue is that a formal postcondition `result ≥ a ∧ result ≥ b ∧ result ≥ c` admits result=999. The missing constraint is `result ∈ {a,b,c}`. This is a spec-completeness problem, not an NL-ambiguity problem. Question-based elicitation cannot reach it.

*Not fixable by any checklist — requires a different tool (formal output-constraint auditing).*

---

## 5. Key Findings

### Finding 1: The checklist cuts miss rate by 80% with zero regressions.
30 → 6 misses. Every C2-fixed case preserved. 24 new catches. The checklist is strictly additive over free Q&A.

### Finding 2: Four issue types reach 100% detection.
D (definition), Pre (precondition), T (tiebreaker), R (representation) — all 100%. These are exactly the categories the checklist's structure directly targets.

### Finding 3: The false pass rate drops from 82.4% to 3.5%.
The augmentation protocol eliminates ~96% of false passes from the original baseline. Of 226 genuinely insufficient NLs, only 8 would still produce false-passing specs (6 misses + 2 partials).

### Finding 4: The remaining 6 misses define three distinct difficulty ceilings.
- NL ambiguity (2): fixable with better checklist categories
- Example inadequacy (2): partially fixable with explicit confirmations
- Formal spec gap (2): requires fundamentally different tools

### Finding 5: Excluding formal-spec-level issues, the effective miss rate is 4/226 = 1.8%.
The two adv41 cases (output-membership constraint) are not NL ambiguities — they are formal verification concerns beyond the scope of specification elicitation. Against NL-level issues only, the checklist achieves 98.2% fix rate.

---

## 6. Implications

1. **The Iron Rule Checklist works.** It should be the core augmentation protocol.
2. **Remaining NL-level gaps (4 cases) are addressable** by adding a "verify the core operation word" category and explicit confirmation statements for borderline-clear terms.
3. **Formal spec gaps (2 cases) require a separate tool** — output-constraint auditing that checks whether the postcondition admits unintended values. This is complementary to, not a replacement for, the checklist.
4. **The theoretical ceiling for question-based elicitation on this dataset is approximately 98–99% fix rate.** The 2 formal-spec cases represent a structural limitation of the method.
