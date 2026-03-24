---
name: {name}
description: Use when {triggering conditions}
---
# {Title}

## The Iron Law

NO {OUTPUT} WITHOUT {PREREQUISITE} FIRST

This rule is not satisfied by:
- {loophole 1 — e.g., partial prerequisite}
- {loophole 2 — e.g., remembered or assumed prerequisite}
- Skipping ahead and backfilling later
- Treating the output as low-stakes enough to skip

If {PREREQUISITE} does not exist, stop and ask for it.

## Checklist

Complete every step in order. Do not skip or abbreviate.

1. {step 1}
2. {step 2}
3. {step 3}
4. {step 4}
5. {step 5}

## Red Flags — STOP

If you are thinking any of these thoughts, stop immediately:

| Thought | Reality |
|---------|---------|
| "{rationalization 1}" | {counter 1} |
| "{rationalization 2}" | {counter 2} |
| "{rationalization 3}" | {counter 3} |

## Process Flow

```graphviz
digraph {name}_skill {
  rankdir=TB;
  node [shape=box style=rounded];

  START [label="Invoke\n{name}" shape=oval];
  LAW   [label="Iron Law\nCheck" shape=diamond];
  STEP1 [label="{step 1}"];
  STEP2 [label="{step 2}"];
  END   [label="done" shape=oval];

  START -> LAW;
  LAW   -> STEP1  [label="{PREREQUISITE} present"];
  LAW   -> END    [label="missing — STOP" style=dashed];
  STEP1 -> STEP2;
  STEP2 -> END;
}
```

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
