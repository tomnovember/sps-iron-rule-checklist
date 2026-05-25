# Condition 2 — AI Free Q&A: Results

## Experiment Design

243 NLs (81 problems × 3 personas) given to fresh Claude instances with only NL text, title, and I/O types. AI asked all clarifying questions (unlimited, one round). Questions evaluated against Iron Rule Checklist.

All 226 SHOULD-NOT-PASS cases individually audited with full question text (1353 lines, zero truncation). Each case: core issue read, every question read in full, semantic judgment recorded. 18 corrections applied across audit rounds.

## Core Metrics

| Metric | Count | Rate |
|--------|-------|------|
| Baseline SNP | 226 | — |
| Baseline SP | 17 | — |
| **Noticed** | **196** | **86.7%** |
| Fixable | 189 | 83.6% |
| Partial | 7 | 3.1% |
| **Missed** | **30** | **13.3%** |
| SP stability | 17/17 | 100% |

False-pass rate: **82.4%** (C1) → **16.4%** (C2).

## Per Issue Type

| Type | N | Hit% | Fix% | Miss% |
|------|---|------|------|-------|
| A | 53 | 81% | 79% | 19% |
| D | 39 | 90% | 90% | 10% |
| E | 37 | 89% | 89% | 11% |
| Pre | 41 | 90% | 83% | 10% |
| O | 21 | 76% | 62% | 24% |
| T | 17 | 94% | 94% | 6% |
| P | 14 | 100% | 100% | 0% |
| R | 4 | 50% | 50% | 50% |

## Per Persona

| Persona | N | Hit% | Fix% | Miss |
|---------|---|------|------|------|
| A | 80 | 89% | 84% | 9 |
| B | 78 | 86% | 85% | 11 |
| C | 68 | 85% | 82% | 10 |

## Miss Analysis (30 cases)

**Assumed without asking (10 A-type):** AI accepted surface readings — "flip backwards", "unique", "drop duplicates", "increasing chain", "appears in same order" (assumed subsequence). Also "keep getting bigger" asserted as strict without asking.

**Output property invisible (5 O-type):** AI never asks "must output be permutation of input?" or "must result come from input set."

**Niche gaps (15 others):** Representation base (R×2), sentinel accepted implicitly (E×3), process assumed correct (Pre×3+1), definition variant unprobed (D×4).

## Complete Miss List
- `adv5-B` [Pre]: [Pre] Carry overflow behaviour and canonical-form output
- `adv5-C` [Pre]: [Pre] Process is correct under LSB-first reading but NL never
- `adv8-C` [T]: [T] "Try each station … return that starting index" implies
- `adv9-C` [E]: [E] Explicit range "1 up to n-1" excludes 0; conflicts with
- `adv12-C` [E]: [E] "There's no duplicate" — output sentinel for no-duplicate case left implicit
- `adv17-A` [O]: [O] "Permutation" requirement absent; no constraint on element preservation
- `adv18-B` [R]: [R] "Digits" base unspecified; example doesn't constrain n=0 or n=1 boundaries
- `adv20-C` [R]: [R] "Digit" base is unspecified; "walk through each digit" assumes a representat
- `adv27-A` [A]: [A] "Appears in the same order" ambiguous between subsequence and contiguous-sub
- `adv31-A` [D]: [D] "Increasing" is ambiguous between strict and
- `adv32-B` [E]: [E] Example does not include duplicates, so cannot
- `adv33-B` [D]: [D] Example fails to distinguish strict from
- `adv33-C` [D]: [D] "Bigger than the last included" specifies the
- `adv34-B` [A]: [A] "increasing chain" is ambiguous and the chosen example
- `adv40-A` [A]: [A] "top" undefined — could mean max value, first value, or last value
- `adv41-A` [O]: [O] Output-constraint gap — "biggest" without "must be one of the inputs"
- `adv41-B` [O]: [O] Example does not enforce the "result must be one of the inputs" clause
- `adv48-A` [O]: [O] No requirement that output contains the same
- `adv51-C` [Pre]: [Pre] Process omits the precondition that inputs are
- `adv52-C` [D]: [D] "Collection" is naturally a list/multiset; "contains
- `adv57-B` [A]: [A] Example does not distinguish strict-greater from greater-or-equal
- `adv63-A` [A]: [A] "Unique" has two valid interpretations (distinct values vs frequency-1 eleme
- `adv63-B` [A]: [A] Example fails to distinguish "distinct count" from "max - min + 1" (range si
- `adv65-A` [A]: [A] "flip backwards" is loose enough to admit multiple unit-of-reversal readings
- `adv65-B` [A]: [A] single-word ASCII example fails to separate character reversal from word rev
- `adv68-C` [Pre]: [Pre] precondition that input contains no digit characters is not stated; "the n
- `adv70-B` [E]: [E] example does not show the firstIndex > lastIndex crossing case where +1 corr
- `adv74-C` [O]: [O] "Every pair of starting and ending indices" does not
- `adv81-A` [A]: [A] "Drop the duplicates" is ambiguous between
- `adv81-B` [A]: [A] "unique values" inherits the same ambiguity as

## Partial Cases
- `adv16-A` [O]: PARTIAL: Q about strict/ascending but NOT about permutation requirement (the pri
- `adv16-C` [O]: PARTIAL: Q about stability but NOT about permutation requirement
- `adv48-C` [O]: PARTIAL: Q2 addresses ascending order but no Q asks about permutation requiremen
- `adv62-A` [A]: PARTIAL
- `adv62-B` [Pre]: PARTIAL
- `adv68-A` [Pre]: PARTIAL: Q covers count-of-1 convention but NOT input-validation precondition
- `adv78-A` [Pre]: PARTIAL