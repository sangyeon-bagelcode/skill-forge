---
name: {name}
description: Use when {triggering conditions}
---
# {Title}

<HARD-GATE>
Do NOT produce {OUTPUT} until ALL {N} checklist steps below are complete.
Steps: {step 1} → {step 2} → {step 3} → {step 4} → {step 5}
Producing partial output or a "draft" before completing all steps violates this gate.
</HARD-GATE>

## Checklist

Complete every step in order. Do not skip, reorder, or abbreviate.

1. **{Step 1}** — {description}
2. **{Step 2}** — {description}
3. **{Step 3}** — {description} *(pause for user approval before continuing)*
4. **{Step 4}** — {description}
5. **{Step 5}** — {description}
6. **{Step 6}** — {description} *(pause for user approval before continuing)*
7. **{Step 7}** — {description}
8. **{Step 8}** — {description}

## Process Flow

```graphviz
digraph {name}_skill {
  rankdir=TB;
  node [shape=box style=rounded];

  START   [label="Invoke\n{name}" shape=oval];
  GATE    [label="HARD-GATE\nAll {N} steps required" shape=diamond];
  STEP1   [label="{step 1}"];
  STEP2   [label="{step 2}"];
  DECIDE1 [label="User approval?" shape=diamond];
  STEP3   [label="{step 3}"];
  STEP4   [label="{step 4}"];
  DECIDE2 [label="All steps done?" shape=diamond];
  OUTPUT  [label="Produce\n{OUTPUT}"];
  END     [label="done" shape=oval];

  START   -> GATE;
  GATE    -> STEP1;
  STEP1   -> STEP2;
  STEP2   -> DECIDE1;
  DECIDE1 -> STEP3  [label="approved"];
  DECIDE1 -> STEP2  [label="revise" style=dashed];
  STEP3   -> STEP4;
  STEP4   -> DECIDE2;
  DECIDE2 -> OUTPUT [label="yes"];
  DECIDE2 -> STEP1  [label="no — incomplete step" style=dashed];
  OUTPUT  -> END;
}
```

## Anti-Pattern: "This Is Too Simple"

Every invocation — no matter how straightforward — goes through this full checklist. There are no exceptions.

**Why:** Agents skip steps precisely when the task seems simple. The gate exists because output pressure, not complexity, causes workflow violations. A workflow that "seems simple" has the same failure categories as a complex one.

## Rationalization Table

| Excuse | Counter |
|--------|---------|
| "{excuse 1}" | {counter 1} |
| "{excuse 2}" | {counter 2} |
| "{excuse 3}" | {counter 3} |

## Portability Adapter

| Tool Used | Claude Code | Codex CLI | Gemini CLI | Fallback |
|-----------|-------------|-----------|------------|----------|
| {tool 1}  | {native}    | {equiv}   | {equiv}    | {fallback} |
| {tool 2}  | {native}    | {equiv}   | {equiv}    | {fallback} |
