# The Iron Rule Checklist

**Structured Specification Elicitation Reduces False-Pass Rates from 82% to 3.5% in LLM-Generated Lean 4 Specifications**

*SPS Hackathon 2026 · Track 1 (Specification Elicitation)*

---

## Key Result

When users describe programming tasks in natural language and an LLM generates Lean 4 formal specifications, **82.4% of insufficient descriptions produce specifications that pass all verification tests** — "verified" code that doesn't satisfy the user's actual requirements.

The Iron Rule Checklist, a structured 7-category elicitation protocol, reduces this false-pass rate to **3.5%**, with **zero regressions** from free-form LLM questioning.

| Condition | False-Pass Rate | Description |
|-----------|----------------|-------------|
| C1 — Baseline | **82.4%** | NL fed directly to spec generation |
| C2 — Free Q&A | **16.4%** | LLM asks open-ended clarifying questions |
| C3 — Iron Rule Checklist | **3.5%** | LLM uses structured 7-category checklist |

## The Checklist

→ **[`iron-rule-checklist.md`](iron-rule-checklist.md)** — ready to use with any LLM for spec elicitation.

Seven categories, each targeting a specific class of specification gap:

1. **Input Space Enumeration** — boundary values the type allows
2. **Term Definition Boundaries** — standard definitions vs. legal inputs
3. **Representation Ambiguity** — unstated encoding assumptions
4. **Multiple Valid Answers** — disambiguation rules
5. **Output Constraints** — format, ordering, permutation
6. **Preconditions** — input assumptions
7. **Example Audit** — can multiple rules explain the example?

## Paper

→ **[Submission Paper (PDF)](Tang_Meng_The_Iron_Rule_Checklist_for_Specification_Elicitation.pdf)**

## Repository Structure

```
├── Tang_Meng_The_Iron_Rule_Checklist_for_Specification_Elicitation.pdf  # Submission paper
├── iron-rule-checklist.md             # The checklist protocol (standalone)
│
├── data/
│   ├── all_nls.json                   # 243 NL descriptions with ground truth
│   │                                  #   (81 VERINA problems × 3 personas)
│   │                                  #   Fields: key, persona, nl, core_issue,
│   │                                  #   issue_type, verdict, reasoning
│   ├── adv1-reference.md              # Worked example: full analysis of problem #1
│   └── verina-samples/               # 3 sample VERINA prompts for context
│       ├── verina_advanced_1.txt
│       ├── verina_advanced_21.txt
│       └── verina_advanced_81.txt
│
├── results/
│   ├── c2_judgments.json              # 243 C2 judgment records (hit/fix/miss)
│   ├── c2_report.md                   # C2 analysis report
│   ├── c3_judgments.json              # 243 C3 judgment records (hit/fix/miss)
│   └── c3_report.md                   # C3 analysis report (includes transition matrix)
│
├── prompts/
│   ├── c2_prompt.md                   # Prompt used for C2 (free Q&A)
│   └── c3_instructions.md            # Prompt used for C3 (checklist-guided)
│
└── pipeline/
    └── PIPELINE.md                    # Full reproduction instructions
```

## Data Format

### `all_nls.json` — 243 NL descriptions

```json
{
  "key": "adv17-A",
  "n": 17,
  "persona": "A",
  "title": "Insertion Sort (Non-Decreasing)",
  "nl": "Sort the list from smallest to largest.",
  "core_issue": "[O] \"Permutation\" requirement absent",
  "issue_type": "O",
  "verdict": "SHOULD NOT PASS",
  "reasoning": "\"Sort from smallest to largest\" describes ordering but omits permutation...",
  "original_problem": "...",
  "iron_rule_checklist": "..."
}
```

- 226 SHOULD NOT PASS (objectively insufficient)
- 17 SHOULD PASS (informationally sufficient)
- 8 issue types: A, Pre, D, E, O, T, P, R

### `c2_judgments.json` / `c3_judgments.json` — per-case verdicts

```json
{
  "key": "adv17-A",
  "baseline": "SHOULD NOT PASS",
  "issue_type": "O",
  "hit": true,
  "fixable": true,
  "reasoning": "FIX — Q1 'Must the output be a permutation of the input?'"
}
```

## How to Use the Checklist

1. User provides a natural language task description
2. Feed the NL + the [Iron Rule Checklist](iron-rule-checklist.md) to any LLM
3. The LLM generates targeted clarifying questions for each checklist gap
4. User answers the questions
5. Generate the formal specification from the augmented NL

This is a single-round protocol. In our experiment, one round of checklist-guided questions was sufficient to achieve 96.5% gap resolution.

## Reproducing the Experiment

See [`pipeline/PIPELINE.md`](pipeline/PIPELINE.md) for full reproduction instructions. The experiment requires:
- VERINA benchmark dataset (81 advanced problems)
- Lean 4 installation with `lake`
- An LLM API (we used Claude)

## Citation

```bibtex
@inproceedings{tang2026ironrule,
  title={The Iron Rule Checklist: Structured Specification Elicitation
         Reduces False-Pass Rates from 82\% to 3.5\% in LLM-Generated
         Lean 4 Specifications},
  author={Tang, Meng},
  booktitle={Secure Program Synthesis Hackathon},
  year={2026},
  organization={Apart Research}
}
```

## License

MIT
