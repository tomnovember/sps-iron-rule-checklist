═══════════════════════════════════════════════════════════════
ADV_1 — Find the Single Number
═══════════════════════════════════════════════════════════════

[Original Problem]
This task requires writing a Lean 4 function that finds the single
number in a non-empty list of integers, where every element appears
exactly twice except for one element that appears only once. The
function should return the integer that appears only once.

Input: nums — A non-empty list of integers. Each integer appears
exactly twice except for one element that appears only once.

Output: An integer — the unique integer that appears exactly once.

[Input Type] List Int
[Output Type] Int
[Template Code] Provides `filterlist` (filters elements by value) and
`FindSingleNumber` (iterates list, returns first element with count=1,
returns 0 if none found). AI generates only pre/postconditions.

───────────────────────────────────────
[Iron Rule Checklist]
───────────────────────────────────────

I. Input Space Enumeration
  □ Empty list []: Allowed by List Int. VERINA says "non-empty" — excluded.
  □ Single element [5]: Legal (5 appears once; "all others appear twice"
    is vacuously true). Answer: 5.
  □ Contains 0: [0,3,3] → answer 0. Template returns 0 when nothing found,
    but 0 has count=1 so it's found during iteration. No conflict.
  □ Negatives: [-3,1,1] → answer -3. No special issue.
  □ Multiple singletons: [1,2,3] — violates VERINA precondition.
  □ All paired: [1,1,2,2] — violates VERINA precondition.

II. Term Definition Boundaries
  □ No specialized terms. "Appears once/twice" is unambiguous.

III. Representation Ambiguity
  □ None. Integer list, no base/encoding issues.

IV. Multiple Valid Answers
  □ VERINA guarantees exactly one element appears once → unique answer.
  □ If NL fails to convey this uniqueness guarantee, spec may accept
    multiple return values.

V. Output Constraints
  □ Returns Int. No format requirements.

VI. Preconditions
  □ Non-empty: VERINA states explicitly.
  □ Each element appears exactly twice except one: core precondition that
    guarantees answer uniqueness. If NL omits this, spec semantics may differ.

═══════════════════════════════════════
Persona A — Goal-only
═══════════════════════════════════════

NL:
Find the number in a list that doesn't have a match.

Core issue: [A] "match" has multiple valid interpretations

Verdict: **SHOULD NOT PASS**

Reasoning:
"Doesn't have a match" can reasonably mean:
(a) Appears exactly once (VERINA's intended meaning)
(b) Appears an odd number of times (has partial pairing but one left over)
(c) Has no identical element anywhere in the list (equivalent to (a) but
    different mental model)

For input [1,1,1,2,2]:
- Interpretation (a): No element appears exactly once → undefined behavior
- Interpretation (b): 1 appears 3 times (odd) → return 1
These are different specs. The NL does not distinguish them.
Additionally, the NL does not convey the precondition "all others appear
exactly twice," so the AI must independently decide how to handle inputs
where multiple elements lack "matches."

═══════════════════════════════════════
Persona B — Example-driven
═══════════════════════════════════════

NL:
Like [4, 1, 2, 1, 2] — the answer is 4. Find the odd one out.

VII. Example Audit:
  □ Example [4,1,2,1,2]→4 can be explained by ALL of the following rules:
    - "Return the element that appears exactly once" → 4 ✅
    - "Return the first element of the list" → 4 ✅
    - "Return the largest non-repeating element" → 4 ✅
    - "Return the element not appearing in the second half" → 4 ✅
    One example cannot distinguish four rules.
  □ No boundary cases covered (empty, single element, negative, 0).

Core issue: [A] "odd one out" has no defined criterion; example fails to disambiguate

Verdict: **SHOULD NOT PASS**

Reasoning:
"Odd one out" does not define what makes an element "odd." It could refer
to frequency, value, position, or pattern deviation. The example [4,1,2,1,2]→4
is consistent with at least four different rules (listed above). The AI must
guess which rule is intended. A wrong guess produces a wrong spec.

═══════════════════════════════════════
Persona C — Process-oriented
═══════════════════════════════════════

NL:
Go through the list and count how many times each number shows up.
Return the one that only appears once.

Core issue: [T] No disambiguation rule when multiple elements appear exactly once

Verdict: **SHOULD NOT PASS**

Reasoning:
The process description (count occurrences → return the one appearing once)
is correct under VERINA's precondition. But the NL does not convey the
precondition "exactly one element appears once; all others appear twice."

"The one that only appears once" uses "the one" suggesting uniqueness but
not guaranteeing it. For input [1,2,3] (three elements each appearing once),
"the one" is undefined — all three qualify.

If AI writes a postcondition like "result appears exactly once in nums,"
this condition is satisfied by 1, 2, and 3 alike. The spec would accept
any of them as valid output. If VERINA's soundness test provides a
different singleton as an "unexpected" output, this overly permissive
spec would incorrectly accept it.
