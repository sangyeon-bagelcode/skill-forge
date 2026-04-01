---
name: test
description: Use when a skill has been written and needs validation before deployment, or when validating any existing skill's agent compliance
---

# Skill Forge: Test

<HARD-GATE>
Do NOT mark test phase as complete until the user has reviewed eval results
via the HTML viewer and approved (empty feedback = approval).
Steps: write eval prompts → run subagents → draft assertions → grade →
       aggregate benchmark → user review → iterate → (optional) optimize description
Skipping user review or marking complete without benchmark.json violates this gate.
</HARD-GATE>

---

## Checklist

Complete every step in order. Do not skip, reorder, or abbreviate.

### 1. Write eval prompts

Create 2-3 realistic test prompts. Each prompt must be concrete and detailed — include
file paths, personal context, backstory. Never abstract or generic.

Save to `evals/evals.json` with this schema:

```json
{
  "skill_name": "<name>",
  "evals": [
    {
      "id": "eval-01",
      "prompt": "Full realistic user prompt with context",
      "expected_output": "What a correct response looks like",
      "files": ["paths/to/relevant/files"],
      "expectations": [
        "Specific verifiable expectation 1",
        "Specific verifiable expectation 2"
      ]
    }
  ]
}
```

Good prompts simulate real usage pressure — ambiguity, time constraints, conflicting
priorities. Bad prompts are clean-room abstractions that never occur in practice.

### 2. Spawn all runs in the same turn

For each eval, spawn TWO subagents simultaneously in the same turn:

- **with-skill** — Provide the skill path in the subagent's system prompt.
- **without-skill** — Baseline run with no skill injected.

Save outputs to:
```
<workspace>/iteration-N/eval-<ID>/with_skill/outputs/
<workspace>/iteration-N/eval-<ID>/without_skill/outputs/
```

Write `eval_metadata.json` per eval with run configuration and status.

Capture `timing.json` from subagent completion notifications immediately — do not
defer timing capture.

### 3. Draft assertions while runs execute

Do not wait idle for subagent runs to complete. While runs execute, draft assertions.

Rules for assertions:
- **Quantitative and objectively verifiable** — "file X exists", "output contains Y",
  "no files outside directory Z were modified".
- **Subjective quality** is NOT an assertion — route those to qualitative user review
  in the HTML viewer instead.

Update `eval_metadata.json` and `evals/evals.json` with the drafted assertions.

### 4. Grade each run

Spawn a grader subagent per run using [`agents/grader.md`](agents/grader.md).

Each grader produces `grading.json`:

```json
{
  "text": "Human-readable grading summary",
  "passed": true,
  "evidence": ["Specific evidence for each assertion"]
}
```

For programmatically verifiable assertions (file exists, string match, etc.), run a
script instead of relying on the grader's judgment.

### 5. Aggregate benchmark + analyze

Run the aggregation script:

```bash
python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
```

This produces:
- `benchmark.json` — structured results for all evals
- `benchmark.md` — human-readable summary

Then run the analyst pass using [`agents/analyzer.md`](agents/analyzer.md) to identify
patterns:
- Non-discriminating assertions (pass both with and without skill)
- High-variance results across evals
- Time/token tradeoffs between with-skill and without-skill runs

### 6. Launch HTML viewer for user review

Generate the review interface:

```bash
python eval-viewer/generate_review.py --benchmark <workspace>/iteration-N/benchmark.json
```

For iteration 2+, include the previous workspace for comparison:

```bash
python eval-viewer/generate_review.py \
  --benchmark <workspace>/iteration-N/benchmark.json \
  --previous-workspace <workspace>/iteration-(N-1)
```

For headless / CI environments, add `--static` to generate a self-contained HTML file.

Tell the user:
- **Outputs tab** — side-by-side with-skill vs. without-skill outputs for qualitative review
- **Benchmark tab** — quantitative assertion results and timing data

Wait for the user to review. Read `feedback.json` after they close the viewer.

### 7. Read feedback and iterate

Read `feedback.json` from the viewer output directory.

- **Empty feedback** = user approves. Proceed to step 8 or finalize.
- **Non-empty feedback** = focus only on the complaints. Do not re-examine passing areas.

When applying fixes, follow the Writing Philosophy:
- **Generalize** — fix the pattern, not just the instance
- **Lean** — remove words that don't change behavior
- **Why** — if adding a rule, explain why it matters
- **Bundle** — group related fixes into a single skill edit

Rerun into `iteration-N+1/`. Repeat steps 2-7 until the user approves.

### 8. Description optimization (optional)

After the skill body is finalized and approved, optimize the `description` field in
the frontmatter for accurate trigger matching.

1. Generate 20 trigger evals — 10 prompts that SHOULD trigger the skill, 10 that
   SHOULD NOT.
2. Review the trigger evals via [`assets/eval_review.html`](assets/eval_review.html).
3. Run the optimization loop:
   ```bash
   python -m scripts.run_loop --skill-path <path> --max-iterations 5
   ```
4. Apply `best_description` from the output to the skill's frontmatter.

---

## Workspace Structure

```
<workspace>/
  evals/
    evals.json                    # eval prompts + expectations
  iteration-1/
    eval-01/
      eval_metadata.json          # run config, assertions, status
      with_skill/
        outputs/                  # subagent output files
        timing.json               # wall time, token count
        grading.json              # grader results
      without_skill/
        outputs/
        timing.json
        grading.json
    eval-02/
      ...
    benchmark.json                # aggregated results
    benchmark.md                  # human-readable summary
    analysis.md                   # analyzer agent output
  iteration-2/
    ...
  feedback.json                   # from HTML viewer
```

---

## Blind Comparison (optional advanced feature)

For high-stakes skills, use [`agents/comparator.md`](agents/comparator.md) to run a
blind comparison. The comparator receives both outputs (with-skill and without-skill)
in randomized order without labels and evaluates which is better. This eliminates
label bias in qualitative assessment.

---

## Environment Adaptation

| Environment | Adaptation |
|-------------|-----------|
| **Cowork / headless** | Use `--static` flag with `generate_review.py` to produce a self-contained HTML file. Share the file path with the user. |
| **Claude.ai** (no subagents) | Run evals sequentially in the same session. Execute with-skill first, capture output, then without-skill. No parallel spawning. |
| **Timeout handling** | If a subagent run exceeds 5 minutes, capture partial output and mark the eval as `timed_out` in `eval_metadata.json`. Still grade what was produced. |

---

## Reference Files

| File | Purpose |
|------|---------|
| [`agents/grader.md`](agents/grader.md) | Grading agent — scores individual eval runs against assertions |
| [`agents/analyzer.md`](agents/analyzer.md) | Analyst agent — finds patterns across evals (non-discriminating, high-variance) |
| [`agents/comparator.md`](agents/comparator.md) | Blind comparison agent — label-free qualitative comparison |
| [`references/schemas.md`](references/schemas.md) | JSON schemas for evals.json, eval_metadata.json, grading.json, benchmark.json |

---

## Rationalization Table

These are the rationalizations an agent is most likely to use to bypass the test
process. Each one has been observed or predicted; each one is invalid.

| Rationalization | Why It Is Wrong |
|----------------|-----------------|
| "The skill looks good, we can skip evals" | Looking good is not evidence. The old test skill relied on heuristic self-assessment and consistently overestimated compliance. Actual execution is the only valid signal. |
| "Baselines are wasteful — just test with the skill" | Without a baseline, you cannot distinguish skill-caused behavior from behavior the agent would produce anyway. Non-discriminating assertions waste more time than baselines do. |
| "Skip the viewer, just summarize the results" | Summaries compress away the qualitative signals that only a human can evaluate — tone, structure, judgment calls. The viewer exists because summarization loses information. |
| "Description optimization is too slow, the current one is fine" | A poorly matched description causes the skill to trigger on wrong prompts or miss correct ones. Five iterations of optimization takes minutes; mis-triggering wastes hours. |
| "I can grade myself without the grader agent" | Self-grading is the exact problem this system was designed to eliminate. The grader agent exists to provide independent evaluation. Use it. |

---

## Portability Adapter

| Tool / Feature | Platform Gap | Fallback |
|----------------|-------------|---------|
| Agent (subagent dispatch) | Codex, API-only, some Claude.ai contexts | Run evals sequentially in the same session. Capture each output before starting the next. Document results inline. |
| Bash / shell execution | Claude.ai web, some API deployments | Replace script invocations with manual steps: read output files, compute aggregates by hand, write benchmark.json directly. |
| File system (Read/Write/Edit) | Pure API / stateless contexts | Hold intermediate results in conversation context. Ask the user to paste file contents if needed. |
| `generate_review.py` | No Python runtime, or no browser access | Generate a markdown summary of results instead. Include side-by-side output snippets. Ask the user to review inline. |
| `run_loop.py` | No Python runtime | Manually iterate: edit description, run trigger evals, check results, repeat up to 5 times. |
