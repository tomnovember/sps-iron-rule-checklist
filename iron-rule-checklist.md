# The Iron Rule Checklist

A structured 7-category specification elicitation protocol for NL-to-formal-spec pipelines.

**How to use:** Before generating a formal specification from a natural language description, apply every category below to the NL. For each item, either (a) confirm the NL provides sufficient information, or (b) generate a clarifying question. No item may be skipped.

---

## I. Input Space Enumeration

- List all boundary values allowed by the input type (0, negatives, empty list/array, single element, very large, duplicates).
- For each boundary value: does the NL define behavior?
- If not: what reasonable interpretations exist for that boundary?

## II. Term Definition Boundaries

- Does the NL use terms with standard definitions (subsequence, palindrome, Armstrong number, majority element)?
- Does the standard definition cover ALL values allowed by the input type?
  - Example: "Armstrong number" standard definition covers positive integers only, but input type `Nat` includes 0.
- For values not covered: does the NL provide supplementary rules?

## III. Representation Ambiguity

- Do concepts like "digit", "character", or "bit" depend on a specific representation?
  - Example: "has the digit 8" — decimal digit? Or current base? `0x81` = decimal 129, whose decimal digits are 1, 2, 9 — no 8.
- Is the representation explicitly stated?

## IV. Multiple Valid Answers

- Are there inputs with more than one valid answer?
- If yes: what disambiguation rule does the NL specify?
  - Examples: return first in list if tied, lexicographically smallest pair, any valid answer.
- If no rule: the spec will choose one arbitrarily — is that acceptable?

## V. Output Constraints

- What format, ordering, or structural requirements does the task impose?
  - Examples: indices must be sorted, result in ascending order, output must be a permutation of input.
- Does the NL cover these requirements?
  - Critical: "sort a list" without "output is a permutation of input" admits fabricated outputs.

## VI. Preconditions

- What assumptions does the task make about input?
  - Examples: non-empty, no duplicates, sorted, each element appears exactly twice, all positive.
- Does the NL state these assumptions?
- If unstated: the LLM must decide behavior for inputs violating assumptions — different decisions lead to different specs.

## VII. Example Audit (for example-driven descriptions)

- Can the example be explained by multiple different rules?
  - List each possible rule. Verify each explains the example.
- If multiple rules all explain the example → the example fails to disambiguate → the NL has insufficient information.
- Does the example cover boundary cases?
- Does the example accidentally mislead?

---

## Issue Type Taxonomy

When applying the checklist, tag each identified gap with an issue type:

| Type | Meaning | Checklist Category |
|------|---------|-------------------|
| A | Lexical ambiguity | II (peripheral), VII |
| Pre | Precondition omission | VI |
| D | Definition boundary gap | II |
| E | Edge case omission | I |
| O | Output constraint omission | V |
| T | Tiebreaker omission | IV |
| P | Preprocessing omission | I, II |
| R | Representation ambiguity | III |

## Citation

If you use this checklist, please cite:

> Tang, M. (2026). The Iron Rule Checklist: Structured Specification Elicitation Reduces False-Pass Rates from 82% to 3.5% in LLM-Generated Lean 4 Specifications. Secure Program Synthesis Hackathon.
