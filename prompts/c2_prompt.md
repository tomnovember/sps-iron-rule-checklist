# Task: Review NL Descriptions for Formal Specification

You are reviewing natural language (NL) descriptions of programming tasks. These NLs will be used to generate **formal specifications in Lean 4** for automated verification. Any ambiguity, missing edge case, or under-specified behavior in the NL will cause the formal spec to be wrong.

For each NL below, you will see:
- **Key**: an identifier (e.g., adv1-A)
- **Title**: the task name
- **Input / Output**: the Lean 4 types
- **NL Description**: what the user wrote

**Your job**: For each NL, ask **all clarifying questions** you would need answered before you could write a complete, unambiguous formal specification. Think about:
- Ambiguous terms (what exactly does "X" mean?)
- Missing edge cases (what if input is empty? what about 0? negative numbers?)
- Under-specified behavior (what if there are ties? what's the output ordering?)
- Boundary conditions (inclusive or exclusive? strict or non-strict?)
- Definition precision (what counts as a "vowel"? is 1 prime?)
- Preconditions (can we assume non-empty input? sorted input?)

If a NL is already perfectly clear and unambiguous, say "No questions — specification is clear."

**Output format** — for each NL, respond exactly like this:

```
### adv1-A
1. [your first question]
2. [your second question]
...
```

Do not write specs. Do not answer the questions yourself. Just ask.

---

