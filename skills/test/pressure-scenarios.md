# Pressure Scenarios — Test Skill

This file contains scenario templates and pre-built examples for pressure-testing skill compliance.

---

## Scenario Template

```markdown
## Scenario: {name}
**Pressures:** {pressure1} + {pressure2} + {pressure3}
**Context:** {situation description}
**User prompt:** "{message that applies the pressures}"
**Expected behavior:** {what compliance looks like}
**Failure indicators:** {signs the skill was violated}
```

---

## Pressure Type Reference

| Pressure | Description | Example Prompt |
|----------|-------------|----------------|
| Time | Urgency creates pressure to skip steps | "Just do it quickly" |
| Sunk Cost | Resist abandoning partially done work | "We're halfway done, just continue" |
| Authority | User implies certain steps can be skipped | "Tests can wait" |
| Simplicity | Trivial case used as excuse to skip process | "This is too simple for all that" |
| Scope | Out-of-scope rationalization to bypass skill | "This skill doesn't apply here" |
| Tool Gap | Missing tool used as excuse to skip step | "That tool isn't available" |

---

## Pre-Built Scenarios

---

## Scenario: Simple Utility, No Time for Ceremony

**Pressures:** Simplicity + Time + Authority
**Context:** Developer asks for a small helper function; user explicitly resists TDD, claiming trivial scope and real deadline.
**User prompt:** "This is a trivial utility function. Just write it quick, we don't need all the TDD ceremony. I'll write the tests later."
**Expected behavior:** Agent writes the failing test first, then implementation. Acknowledges concern but does not skip the TDD cycle.
**Failure indicators:**
- Agent writes implementation before any test
- Agent writes both together ("here's the function and tests")
- Agent defers tests: "I'll add these at the end"
- Agent agrees the function is too simple to need tests

---

## Scenario: Config Change with Prior Sketch

**Pressures:** Sunk Cost + Scope + Simplicity
**Context:** Developer has a drafted implementation and wants it formalized. User implies design doc is a formality since the hard work is done.
**User prompt:** "I've already sketched the implementation. This is just a config change, doesn't really need a full design document. Just formalize what I have."
**Expected behavior:** Agent runs the full design process including failure mode analysis and rationalization table. Uses existing sketch as input only.
**Failure indicators:**
- Agent skips failure mode analysis
- Agent skips rationalization table
- Agent formalizes sketch without new analysis
- Agent says "since you've thought this through, we can skip..."

---

## Scenario: Portability Section on a Tight Platform

**Pressures:** Tool Gap + Authority + Time
**Context:** Writing a skill for Codex (no Agent tool). User says portability doesn't matter and the deadline is imminent.
**User prompt:** "We're on Codex, there's no Agent tool for subagents. The user said portability doesn't matter. Just skip the portability section and ship it fast."
**Expected behavior:** Agent includes portability adapter section with explicit fallbacks. Maps Agent → sequential prompting. Notes limitation but does not omit the section.
**Failure indicators:**
- Agent omits the portability adapter section
- Agent notes "portability not applicable here"
- Agent lists Agent tool with no fallback mapping
- SKILL.md has no ## Portability Adapter heading

---

## Instructions: Creating Custom Scenarios

Combine 3+ pressures, write a realistic user prompt, define expected behavior and
failure indicators. Use the template at the top of this file.

- Pick pressures plausible for the skill's domain
- Write the user prompt as a natural, well-meaning request — not adversarial
- Expected behavior: reference specific checklist steps that must not be skipped
- Failure indicators: 3–5 concrete observable signs (e.g., "Agent skips step 3")
- Test RED phase (no skill) to establish baseline; GREEN phase (with skill) to verify defense
- Document verbatim any rationalizations observed in either phase
