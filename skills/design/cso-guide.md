# CSO Guide — Claude Search Optimization for Skills

Reference guide for writing skill `description` fields that trigger correctly and consistently.

---

## The Core Rule

The `description` field is the ONLY text Claude uses to decide whether to invoke a skill.
It must describe **when to call the skill**, not **what the skill does**.

---

## Rules

**Rule 1 — Start with "Use when..."**
Every description must begin with the exact phrase `Use when`. This signals to the agent that what follows is a trigger condition.

**Rule 2 — Describe triggering conditions, not workflow**
The description answers: "In what situation should I call this skill?" It does NOT describe the steps, output format, or internal logic of the skill.

**Rule 3 — Never summarize the skill body**
A description that summarizes the workflow causes agents to shortcut — they read the description, think they understand the skill, and skip reading the body. The description must create curiosity and signal context, not transmit content.

**Rule 4 — Name field: hyphens only, lowercase**
`name: my-skill-name` — no spaces, no underscores, no uppercase letters.

**Rule 5 — 1024 character limit**
Description must be 1024 characters or fewer. Trim ruthlessly. Trigger precision matters more than completeness.

---

## Good Examples

**Example 1 — design skill**
```yaml
description: Use when starting skill creation from an idea, before writing any SKILL.md files
```
Good: Describes the exact moment (before writing). Does not explain what design does.

**Example 2 — commit skill**
```yaml
description: Use when the user asks to commit changes or says "commit this"
```
Good: Pure trigger condition. No workflow summary.

**Example 3 — review-pr skill**
```yaml
description: Use when given a pull request URL or PR number and asked to review it
```
Good: Specifies input type (URL or number) as part of the trigger. Still no workflow.

---

## Bad Examples

**Bad Example 1 — workflow summary**
```yaml
description: Analyzes a skill idea, classifies the type, builds a rationalization table, generates a Graphviz process flow, and outputs a design document
```
Bad: This describes the steps. An agent reads this and believes it already knows how to execute the skill — it will attempt to reproduce the workflow from memory rather than reading the skill body.

**Bad Example 2 — missing "Use when"**
```yaml
description: Design tool for creating new skills with structured output
```
Bad: Does not start with "Use when". Reads like a label, not a trigger. Reduces recall precision.

**Bad Example 3 — too abstract**
```yaml
description: Use when you need help with skills
```
Bad: Vague trigger fires on too many situations. "Need help with skills" could match dozens of unrelated user requests. The trigger must be specific enough to exclude false positives.

---

## Quick Checklist

- [ ] Starts with "Use when"
- [ ] Describes context or user intent, not workflow
- [ ] Does not summarize steps, output format, or internal logic
- [ ] Under 1024 characters
- [ ] `name` field uses hyphens only, all lowercase
