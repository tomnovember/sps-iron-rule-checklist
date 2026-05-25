# Pipeline — Full Experiment Execution

## Directory Structure

```
cc-package/
├── INSTRUCTIONS.md              ← Step 1 instructions for NL analysis
├── verina-prompts/ (81 files)   ← VERINA original prompts
├── reference/adv1-reference.md  ← Quality reference
├── scripts/
│   ├── extract_nls.py           ← Step 2a: parse NLs from analysis
│   ├── build_prompts.py         ← Step 2b: assemble spec-gen prompts
│   ├── generate_specs.py        ← Step 3: API calls to generate specs
│   ├── combine.py               ← Step 4: merge spec + VERINA tests
│   └── compare_results.py       ← Step 6: predicted vs actual
├── output/                      ← Step 1 output (81 adv{N}.md files)
├── prompts/                     ← Step 2 output (243 prompt files)
├── specs/                       ← Step 3 output (243 .lean specs)
├── combined/                    ← Step 4 output (243 combined .lean)
└── results/                     ← Step 5 output (evaluation JSON)
```

## Step 1: Generate NL Analysis (CC, 9 parallel sub-agents)

```bash
cd cc-package
claude
```

Prompt:
```
Read INSTRUCTIONS.md and reference/adv1-reference.md completely first.
Then process all 81 VERINA problems using 9 parallel sub-agents. Each
sub-agent must read INSTRUCTIONS.md and reference/adv1-reference.md,
then process its assigned verina-prompts/ files and write output to
output/adv{N}.md. Do not skip any checklist item. Do not rush.

Sub-agent 1: verina_advanced_1.txt – verina_advanced_9.txt
Sub-agent 2: verina_advanced_10.txt – verina_advanced_18.txt
Sub-agent 3: verina_advanced_19.txt – verina_advanced_27.txt
Sub-agent 4: verina_advanced_28.txt – verina_advanced_36.txt
Sub-agent 5: verina_advanced_37.txt – verina_advanced_45.txt
Sub-agent 6: verina_advanced_46.txt – verina_advanced_54.txt
Sub-agent 7: verina_advanced_55.txt – verina_advanced_63.txt
Sub-agent 8: verina_advanced_64.txt – verina_advanced_72.txt
Sub-agent 9: verina_advanced_73.txt – verina_advanced_81.txt
```

**Output:** 81 files in `output/` (adv1.md through adv81.md)

## Step 2: Extract NLs + Build Prompts

```bash
cd cc-package
python3 scripts/extract_nls.py          # → extracted_nls.json
python3 scripts/build_prompts.py        # → prompts/adv{N}-{P}.txt (243 files)
```

## Step 3: Generate Specs (CC, 9 parallel sub-agents)

Each sub-agent runs the generate script for its persona × range.
**Independence guaranteed:** each spec is a separate API call with no context.

```bash
cd cc-package
claude
```

Prompt:
```
Run 9 parallel tasks to generate Lean specs. Each task runs the
generate_specs.py script with different parameters. Ensure ANTHROPIC_API_KEY
is set.

Task 1: python3 scripts/generate_specs.py --range 1-27 --persona A
Task 2: python3 scripts/generate_specs.py --range 1-27 --persona B
Task 3: python3 scripts/generate_specs.py --range 1-27 --persona C
Task 4: python3 scripts/generate_specs.py --range 28-54 --persona A
Task 5: python3 scripts/generate_specs.py --range 28-54 --persona B
Task 6: python3 scripts/generate_specs.py --range 28-54 --persona C
Task 7: python3 scripts/generate_specs.py --range 55-81 --persona A
Task 8: python3 scripts/generate_specs.py --range 55-81 --persona B
Task 9: python3 scripts/generate_specs.py --range 55-81 --persona C
```

**Output:** 243 files in `specs/` (adv1-A.lean through adv81-C.lean)

## Step 4: Combine Specs with VERINA Tests

**Requires:** VERINA dataset with task.json + test.json per problem.
Set VERINA_DATA to the path of your verina/datasets/verina/ directory.

```bash
export VERINA_DATA="/path/to/verina/datasets/verina"
python3 scripts/combine.py              # → combined/adv{N}-{P}.lean (243 files)
```

## Step 5: Evaluate (WSL, lake build)

Use the existing evaluate.py (adapted for new naming) to run each
combined file through lake build and record pass/fail.

```bash
# In WSL where lake is installed:
python3 evaluate.py \
  --combined-root combined/ \
  --mode decide_10M \
  --results results/results.json
```

**Output:** results/results.json with 243 records

## Step 6: Compare Predicted vs Actual

```bash
python3 scripts/compare_results.py \
  --results results/results.json \
  --output-dir output/ \
  --report comparison_report.md
```

**Output:** comparison_report.md with confusion matrix, per-persona and
per-issue-type breakdowns, and false pass details.

## Naming Convention

| Item | Format | Example |
|------|--------|---------|
| NL analysis | `output/adv{N}.md` | `output/adv55.md` |
| Spec prompt | `prompts/adv{N}-{P}.txt` | `prompts/adv55-A.txt` |
| Generated spec | `specs/adv{N}-{P}.lean` | `specs/adv55-A.lean` |
| Combined file | `combined/adv{N}-{P}.lean` | `combined/adv55-A.lean` |
| Results | `results/results.json` | single file, 243 records |

No zero-padding. Persona codes: A, B, C.
