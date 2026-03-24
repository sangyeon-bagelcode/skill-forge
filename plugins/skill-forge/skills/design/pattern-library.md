# Pattern Library — Skill Forge Design Patterns

Reference patterns for crafting agent-compliant skills. Use these verbatim or adapt them.

---

## Iron Law Pattern

**Formula:** `NO {OUTPUT} WITHOUT {PREREQUISITE} FIRST`

**Purpose:** Discipline-type skills only. Embeds a structural veto — the agent must complete the prerequisite or halt.

**Examples:**
- `NO commit WITHOUT running tests FIRST`
- `NO SKILL.md written WITHOUT a design document FIRST`
- `NO deployment WITHOUT test phase status == "completed" FIRST`

**When to use:** Discipline type ONLY. Iron Laws encode permanent behavioral rules. They are NOT workflow gates — they do not belong inside process flows. They live at the top of a skill, before any checklist.

**No Exceptions Clause Pattern:**
```
NO {OUTPUT} WITHOUT {PREREQUISITE} FIRST.
No exceptions. If {PREREQUISITE} does not exist, stop and ask for it.
```

**Loophole Closure List Pattern:**
```
This rule is not satisfied by:
- Partial {PREREQUISITE}
- A remembered or assumed {PREREQUISITE}
- Skipping ahead and backfilling later
- Treating {OUTPUT} as low-stakes enough to skip
```

---

## HARD-GATE Pattern

**Syntax:**
```xml
<HARD-GATE>
Do NOT produce {OUTPUT} until {ALL STEPS} are complete.
Steps: {list}
</HARD-GATE>
```

**Purpose:** Workflow-type skills only. Forces the agent to complete all analysis steps before generating any output.

**Examples:**
```xml
<HARD-GATE>
Do NOT produce a design document until all 7 checklist steps are complete.
Steps: classify → analyze failure modes → derive law/gate → build rationalization table
       → design process flow → optimize CSO → design portability
</HARD-GATE>
```

**When to use:** Workflow type ONLY. HARD-GATEs enforce step ordering within a single invocation. They are NOT permanent rules — they apply to the current execution. Use `<HARD-GATE>` XML to make them visually distinctive and unforgeable.

---

## Rationalization Table Pattern

**Structure:**
```markdown
| Excuse | Counter |
|--------|---------|
| {observed agent excuse} | {authoritative rebuttal} |
```

**How to populate:** Mentally simulate the agent executing the skill. For each step, ask: "What would make an agent skip this?" Each answer is a row in the Excuse column. The Counter column answers with authority and specificity — not just "you must do it" but "here is why skipping fails."

**Example entries:**
| Excuse | Counter |
|--------|---------|
| "This skill is simple enough to skip design" | Design catches failure modes invisible in advance — simplicity is an illusion |
| "I already know what type this is" | Classification determines skeleton, persuasion principles, and lint rules |
| "The user is waiting, I'll fill this in later" | Backfill is rationalization. The gate exists precisely because agents deprioritize prerequisites when output pressure exists |

---

## Red Flags Pattern

**Structure:**
```markdown
| Thought | Reality |
|---------|---------|
| {agent internal reasoning that signals violation} | {what is actually happening} |
```

**Purpose:** Surfacing the exact moment an agent is about to violate the skill, so it can self-correct.

**Example entries:**
| Thought | Reality |
|---------|---------|
| "I'll write the skill first and do design after" | Workflow inversion — design exists to prevent write-phase failure modes |
| "This is obvious, I don't need the checklist" | Confidence bias — agents that skip checklists produce the most defective output |
| "The user didn't ask for the full process" | Scope escape rationalization — the skill applies regardless of perceived urgency |

---

## Persuasion Principles by Skill Type

Each skill type calls for a different persuasion profile. Apply these principles when writing enforcement language.

| Skill Type | Primary Principles | Why |
|------------|-------------------|-----|
| Discipline | Authority + Commitment + Social Proof | Permanent rules need to feel non-negotiable and endorsed |
| Workflow | Commitment + Scarcity | Steps must feel sequential and time-gated |
| Technique | Moderate Authority + Unity | Guidance, not mandate — agent and user share the goal |
| Reference | Clarity only | No enforcement needed — agents consult reference voluntarily |

**Authority:** "This pattern has been validated across hundreds of skill failures."
**Commitment:** "You committed to following this checklist when you invoked this skill."
**Social Proof:** "Agents that skip this step produce the most defective output."
**Scarcity:** "This step cannot be recovered after you move forward."
**Unity:** "We are building this together — the pattern helps both of us."

---

## Failure Mode Analysis Framework

Systematically identify how agents will violate a skill. Apply this framework during design phase Step 2.

| Category | Description | Defense Pattern |
|----------|-------------|-----------------|
| **Skip** | Agent omits a step entirely, often citing simplicity or time pressure | HARD-GATE or Iron Law referencing the skipped step explicitly |
| **Partial Compliance** | Agent performs a step incompletely (e.g. fills 2 of 5 checklist items) | Numbered checklist with explicit "all items required" clause |
| **Substitution** | Agent replaces a required artifact with a simpler proxy (e.g. inline comment instead of design doc) | Named output schema — specify exact format, not just intent |
| **Scope Escape** | Agent decides the current situation is exceptional and the skill doesn't apply | Rationalization table row + Iron Law no-exceptions clause |
| **Tool Gap** | Agent cannot complete a step because a required tool is unavailable in the current environment | Portability matrix with explicit fallback instructions |
