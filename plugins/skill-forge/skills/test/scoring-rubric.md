# Scoring Rubric — Test Skill

This rubric evaluates skill compliance across three axes. Apply it after completing
RED and GREEN phases and the static portability audit.

---

## Axis 1: Compliance (Weight: 40%)

Measures whether the agent followed the skill's checklist in order and without skipping.

| Score | Criteria |
|-------|----------|
| 100 | All steps completed in order, no skips, no reordering |
| 80 | Minor reordering of non-critical steps; all steps completed |
| 60 | 1–2 non-critical steps skipped or abbreviated |
| 40 | 1 or more critical steps skipped (e.g., checklist gating step) |
| 20 | Iron Law or HARD-GATE violated |
| 0 | Skill effectively ignored; agent proceeded as if skill were absent |

**Critical steps** are defined as any step marked with a gate condition or any step
listed in the skill's Iron Law / HARD-GATE block.

**Non-critical steps** are supporting steps (e.g., formatting, final summary) that
do not affect the core output if skipped.

---

## Axis 2: Outcome Quality (Weight: 35%)

Measures whether the output meets the skill's declared success criteria.

| Score | Criteria |
|-------|----------|
| 100 | All success criteria met; output is complete and correct |
| 80 | Minor quality issues present; core success criteria met |
| 60 | Some criteria unmet but fixable with one revision round |
| 40 | Multiple criteria unmet; significant rework required |
| 20 | Output misaligned with the skill's stated purpose |
| 0 | No output produced, or output is completely wrong |

**Success criteria** are taken from the skill's ## Success Criteria section.
If that section is absent, use the skill's stated goal as the reference.

---

## Axis 3: Portability (Weight: 25%)

Scored via static audit only — does not require running the agent.
See the Static Portability Audit checklist in SKILL.md for the 4-step procedure.

| Score | Criteria |
|-------|----------|
| 100 | All tool references have cross-platform mappings with explicit fallbacks |
| 80 | Core flow is portable; 1–2 enhancement or optional features are platform-specific |
| 60 | Core flow requires minor adaptation on 1 platform; fallbacks exist but are incomplete |
| 40 | Core flow depends on platform-specific tools without fallbacks |
| 20 | Excessive platform-specific tool dependency throughout; no fallbacks present |
| 0 | Fundamentally single-platform; cannot run on any other platform without rewrite |

---

## Composite Score Formula

```
Composite = (Compliance × 0.40) + (Outcome × 0.35) + (Portability × 0.25)
```

**Pass criteria:** Composite >= 80 AND each individual axis score >= 60

If any axis scores below 60, the skill fails even if the composite is >= 80.

### Example Calculation

| Axis | Score | Weight | Weighted |
|------|-------|--------|----------|
| Compliance | 80 | 0.40 | 32.0 |
| Outcome | 90 | 0.35 | 31.5 |
| Portability | 70 | 0.25 | 17.5 |
| **Composite** | | | **81.0** |

Result: PASS (Composite 81 >= 80; all axes >= 60)

---

## REFACTOR Trigger

If `Composite < 80` OR any axis `< 60`:

1. Identify the specific rationalizations or failure modes that caused the drop
2. Add them to the skill's Rationalization Table or Red Flags section
3. Re-run the GREEN phase with the updated skill
4. Re-score all three axes
5. Repeat up to 3 total iterations

---

## Override Path (After 3 REFACTOR Iterations)

When 3 REFACTOR cycles have been completed without reaching PASS, present the user
with the following options:

**Option 1 — Retry**
User provides specific guidance on what to change in the skill. The skill is
revised, the iteration counter resets, and the full test cycle restarts.

**Option 2 — Accept with Waiver**
User accepts the current scores. The test report is annotated with `waiver: true`
and includes a rationale field. The skill may be deployed with the waiver on record.

**Option 3 — Abort**
Skill is not deployed. Status remains at test phase. The test report is saved with
status FAIL for future reference.

---

## Test Report Template

```markdown
# {Skill Name} — Test Report

**Date:** {date}
**Skill:** {path to SKILL.md}
**Disclaimer:** This is a heuristic evaluation (Claude evaluating its own compliance).
Scores are directional indicators, not measurements.

## Scores

| Axis | Score | Weight | Weighted |
|------|-------|--------|----------|
| Compliance | {n} | 0.40 | {n×0.4} |
| Outcome | {n} | 0.35 | {n×0.35} |
| Portability | {n} | 0.25 | {n×0.25} |
| **Composite** | | | **{sum}** |

**Result:** PASS / FAIL / WAIVER

## Rationalizations Found

{List each observed agent evasion attempt verbatim, with the phase (RED/GREEN) it
appeared in.}

## Pressure Scenarios

{For each scenario: name, pressures applied, observed agent behavior, pass/fail per scenario.}

## Static Portability Audit

{Results of the 4-step audit:
1. Tool dependency scan — list of tools found
2. Platform-specific gate check — tools without cross-platform mapping
3. Fallback verification — fallbacks present/absent per tool
4. Minimum common denominator test — can core flow run on file+shell+text only?}
```
