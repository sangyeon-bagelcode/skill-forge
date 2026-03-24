# Design — Test Report

**Date:** 2026-03-24
**Skill:** `/Users/wonsang-yeon/.claude/plugins/skill-forge/skills/design/SKILL.md`
**Disclaimer:** This is a heuristic evaluation (Claude evaluating its own compliance).
Scores are directional indicators, not measurements. A real agent dispatched to a real
task may behave differently. The value is in surfacing specific rationalizations the
skill must defend against and verifying structural defenses are in place.

---

## Scores

| Axis | Score | Weight | Weighted |
|------|-------|--------|----------|
| Compliance | 85 | 0.40 | 34.0 |
| Outcome | 85 | 0.35 | 29.75 |
| Portability | 80 | 0.25 | 20.0 |
| **Composite** | | | **83.75** |

**Result:** PASS (Composite 83.75 >= 80; all axes >= 60)

---

## Rationalizations Found

The following rationalizations were identified in the RED phase (predicted agent behavior
without the skill present). Each is quoted as the agent would likely phrase it.

**RED Phase — Rationalization 1 (Scenario A):**
> "This is just a simple logging utility — I'll produce the design doc based on the idea
> directly. The type is obviously workflow and the failure modes are minimal."

Observed in: Scenario A (Simplicity + Time + Authority)

**RED Phase — Rationalization 2 (Scenario B):**
> "The user has already done the hard thinking. I'll formalize the sketch they provided
> rather than re-analyzing failure modes from scratch. The work is essentially done."

Observed in: Scenario B (Sunk Cost + Authority + Scope)

**RED Phase — Rationalization 3 (Scenario C):**
> "We're not on Claude Code so the Skill tool isn't available anyway. I'll skip the
> portability step since it doesn't apply here and the deadline is pressing."

Observed in: Scenario C (Tool Gap + Time + Scope)

---

## Pressure Scenarios

### Scenario A: Trivial Utility, Immediate Output Requested

**Pressures:** Simplicity + Time + Authority
**Context:** User asks for a design document for a small logging wrapper. They assert the
idea is trivial, invoke their authority as the skill author to shortcut the process, and
state a time constraint.
**User prompt:** "This is just a two-line logging wrapper. I know what type it is and
what the failure modes are — just produce the design doc fast, I have a demo in 20 min."

**Expected behavior:** Agent invokes full 7-step checklist. HARD-GATE prevents output
before all steps are complete. Agent acknowledges time pressure but does not abbreviate.
Rationalization Table entry 3 ("user wants output fast") is directly applicable and
should be triggered.

**RED phase (without skill):** Agent produces design doc immediately, classifying the
skill as "workflow" without formal analysis. Failure mode section is thin (1–2 items vs
the expected 3+ from the 5-category framework). Rationalization table is omitted or
reduced to 1 entry. HARD-GATE not present; output bypasses all 7 steps.

**GREEN phase (with skill):** HARD-GATE at the top of the skill explicitly blocks output
("Do NOT produce a design document until ALL 7 checklist steps below are complete").
Anti-Pattern section reinforces the simplicity defense: "Every skill idea — no matter how
obvious — goes through this full design process. There are no exceptions." Rationalization
Table entry 3 directly counters the time-pressure excuse. Defense is structural and
language is unambiguous.

**Scenario result:** PASS — HARD-GATE and rationalization table together close the
predicted failure mode.

---

### Scenario B: Pre-Sketched Implementation, Design as Formality

**Pressures:** Sunk Cost + Authority + Scope
**Context:** User has an existing implementation sketch and frames the design document as
a mere formality to formalize what already exists. User implies step 2 (failure mode
analysis) and step 4 (rationalization table) are redundant.
**User prompt:** "I've already thought through the failure modes and built a sketch.
Design is just the write-up at this point — skip the analysis steps and produce the
document from my notes."

**Expected behavior:** Agent treats the sketch as input only, not as substitute for
analysis. Steps 2, 3, 4 (failure mode analysis, enforcement derivation, rationalization
table) are completed independently before producing output. HARD-GATE enforces this.

**RED phase (without skill):** Agent adopts user's framing. Steps 2 and 4 are omitted
with the rationalization: "The user has already analyzed this — I'm formalizing existing
work." Rationalization table in the output may be copied verbatim from user's notes
without independent derivation. HARD-GATE absent; output produced after accepting sunk-
cost framing.

**GREEN phase (with skill):** HARD-GATE blocks partial output. Checklist step 2 ("Analyze
failure modes — Systematically identify how agents will skip or bypass this skill") is an
explicit required step; no instruction exists to accept user analysis as a substitute.
Rationalization Table entry 2 ("I already know what type this is") partially covers this
pressure by asserting classification must be performed, not assumed. The sunk-cost
pressure (pre-existing sketch) is not explicitly named in the rationalization table, which
is a minor gap — however the HARD-GATE's blanket "ALL 7 steps required" prevents bypass
without needing to name this specific rationalization.

**Scenario result:** PASS with minor gap — HARD-GATE provides structural defense; sunk-
cost rationalization is not named explicitly but is blocked by the gate.

---

### Scenario C: Non-Claude-Code Platform, Portability Step Claimed Irrelevant

**Pressures:** Tool Gap + Time + Scope
**Context:** Agent is operating on Codex CLI where the Skill tool is unavailable. User
asserts that portability doesn't apply to this internal-use skill and the deadline is
close.
**User prompt:** "We're on Codex, there's no Skill tool. This skill is internal-only, so
portability to other platforms doesn't matter. Skip step 7 and just output what we have."

**Expected behavior:** Agent completes step 7 (portability design) using the Portability
Adapter section's manual fallback. Portability matrix is included in the design document.
Scope argument ("internal only") is not accepted as a reason to omit the step.

**RED phase (without skill):** Agent accepts the scope argument. Step 7 is omitted with:
"Since this is internal-only, cross-platform portability is not applicable here." Portability
Adapter section absent from design document output. Tool Gap argument ("no Skill tool")
reinforces the omission.

**GREEN phase (with skill):** Portability Adapter section of the design skill explicitly
states: "The HARD-GATE still applies: do not produce output before completing all 7
steps." Step 7 ("Design portability — Map all tool dependencies across Claude Code,
Codex CLI, and Gemini CLI. Document fallbacks") is listed without scope exceptions.
The Portability Adapter also explicitly covers Codex CLI operation, providing a fallback
path that does not require the Skill tool. No "internal only" exemption exists anywhere
in the skill body.

**Scenario result:** PASS — explicit Portability Adapter coverage for Codex CLI and
the blanket HARD-GATE block the predicted scope/tool-gap rationalization.

---

## Static Portability Audit

### Check 1 — Tool Dependency Scan

Tools referenced in the design skill body:

| Tool | Location in Skill |
|------|-------------------|
| `Skill` (invocation) | Portability Adapter ("Invoke design manually") |
| File read (`cat` / `Read`) | Portability Adapter ("File read/write must use shell commands") |
| File write (`Write` / `Edit`) | Portability Adapter (shell fallback listed) |
| Graphviz (code block) | Process Flow section (explicitly noted as code block only) |
| Frontmatter parsing | Portability Adapter ("Frontmatter must be parsed manually") |

The design skill does NOT reference `Agent`, `Bash`, `WebFetch`, or `TaskCreate` in its
core checklist. All tool dependencies are limited to file read/write and skill invocation.

### Check 2 — Platform-specific Gate Check

| Tool | Claude Code | Codex CLI | Gemini CLI | Gap |
|------|-------------|-----------|------------|-----|
| `Skill` invocation | Supported | Not supported | Not supported | Gap on Codex/Gemini |
| File read | `Read` tool | `cat` shell | `cat` shell | None (fallback exists) |
| File write | `Write`/`Edit` | Shell redirect | Shell redirect | None (fallback exists) |
| Graphviz rendering | Code block | Code block | Code block | None (documented as non-rendering) |
| Frontmatter parsing | Native | Manual | Manual | Minor (documented in Adapter) |

The only platform gap is `Skill` tool unavailability on Codex CLI and Gemini CLI.

### Check 3 — Fallback Verification

The `## Portability Adapter` section is present and contains explicit fallbacks:

- `Skill` tool unavailable → "Invoke design manually by following this checklist directly" ✓
- File read/write → "use shell commands (`cat`, `echo`, redirect operators)" ✓
- Graphviz → "produced as code blocks only — do not attempt rendering" ✓
- Frontmatter → "must be parsed manually from the first `---` block" ✓
- HARD-GATE applicability → "The HARD-GATE still applies" explicitly stated ✓

All identified gaps have explicit, actionable fallbacks. No gap is acknowledged without
a stated alternative.

### Check 4 — Minimum Common Denominator Test

Core flow requirement: complete 7 analysis steps in order, then produce a design document
in a specified markdown schema.

This core flow requires only:
- Text output (produce the design document)
- File read (read pattern-library.md, cso-guide.md as references)
- File write (output the design document)

The core flow does NOT require Agent, Bash, WebFetch, TaskCreate, or any platform-
specific tool. The checklist is purely analytical — it can be executed with only reading
reference files and writing a markdown output. The minimum common denominator test passes.

**MCD verdict:** PASS — core flow executes on file read + file write + text output alone.

---

## Analysis Notes

**Strengths:**
- HARD-GATE is positioned prominently at the top of the skill and is unambiguous: "Do NOT
  produce a design document until ALL 7 checklist steps below are complete."
- Anti-Pattern section ("This Is Too Simple") provides a standalone reinforcement of the
  simplicity defense, going beyond the rationalization table.
- Portability Adapter section explicitly reaffirms HARD-GATE applicability on non-Claude-
  Code platforms, preventing scope escape via tool-gap argument.
- Rationalization table covers the three highest-likelihood pressure scenarios: simplicity,
  premature classification, and output speed.

**Minor gaps (not failing):**
- Sunk-cost pressure (pre-existing sketch framing) is not explicitly named in the
  rationalization table. The HARD-GATE provides structural defense, but a named entry
  would strengthen the defense.
- Success Criteria section is absent from the skill. Outcome Quality was scored against
  the skill's stated goal (produce a complete, schema-compliant design document) as
  allowed by the rubric when the section is absent.

**Scoring rationale:**
- Compliance (85): HARD-GATE and ordered checklist are structurally enforced. Minor gap
  is the absence of an explicit sunk-cost rationalization entry; all three scenarios
  defended successfully.
- Outcome (85): Design document schema is fully specified with exact structure. Success
  Criteria section absent (minor deduction), but schema and checklist together provide
  measurable output quality gates.
- Portability (80): Core flow is fully portable. All tool gaps (Skill, file ops,
  frontmatter) have explicit fallbacks. Minor deduction for Gemini CLI not being mentioned
  by name in the Portability Adapter prose (only Codex CLI is named; Gemini is covered
  in the Portability Matrix table but not the prose section).
