# Portability Guide — Cross-Platform Skill Compatibility

Reference for writing skills that work across Claude Code, Codex CLI, and Gemini CLI.

---

## Portability Matrix

| Element | Claude Code | Codex CLI | Gemini CLI |
|---------|-------------|-----------|------------|
| Tool calls | `Edit`, `Bash`, `Read`, `Write` | `edit`, `shell` | `edit`, `shell` |
| Sub-agents | `Agent` tool | None (sequential) | `activate_skill` |
| Skill invocation | `Skill("name")` | Not supported | `activate_skill("name")` |
| Task tracking | `TaskCreate`, `TaskUpdate` | None (comment-based) | None |
| Flow control | Skill chaining via `Skill` tool | Prompt chaining (sequential) | Skill chaining via `activate_skill` |
| File read | `Read` tool | `cat` / shell | `cat` / shell |
| File write | `Write` / `Edit` tool | Shell redirect (`>`, `>>`) | Shell redirect |
| Graphviz rendering | Code block (not rendered) | Code block | Code block |
| Frontmatter parsing | Native | Manual parse (first `---` block) | Manual parse |
| Status files | JSON via `Write` / `Edit` | `echo` / shell redirect | `echo` / shell redirect |

---

## Tool Name Mappings

### File Operations

| Action | Claude Code | Codex CLI | Gemini CLI |
|--------|-------------|-----------|------------|
| Read file | `Read(path)` | `shell: cat path` | `shell: cat path` |
| Write new file | `Write(path, content)` | `shell: cat > path << 'EOF'` | `shell: cat > path << 'EOF'` |
| Edit file (replace) | `Edit(path, old, new)` | `shell: sed -i 's/old/new/' path` | `shell: sed -i 's/old/new/' path` |
| List directory | `Glob(pattern)` | `shell: ls path` | `shell: ls path` |
| Search content | `Grep(pattern)` | `shell: grep -r pattern path` | `shell: grep -r pattern path` |
| Run command | `Bash(cmd)` | `shell: cmd` | `shell: cmd` |

### Agent & Skill Operations

| Action | Claude Code | Codex CLI | Gemini CLI |
|--------|-------------|-----------|------------|
| Invoke sub-skill | `Skill("skill-name")` | Follow skill body manually | `activate_skill("skill-name")` |
| Spawn sub-agent | `Agent(prompt)` | Not available — use sequential steps | `activate_skill` with context |
| Track task | `TaskCreate(...)` | Add comment to output | Not available |

---

## Fallback Patterns

### When `Skill` Tool Is Unavailable (Codex CLI)

Do not attempt to call the skill tool. Instead:
1. Read the target skill's `SKILL.md` file directly with `cat`.
2. Extract the checklist from the `## Checklist` section.
3. Execute each step sequentially in the current prompt context.
4. Record progress as inline comments in the output.

### When `Agent` Tool Is Unavailable (Codex CLI, Gemini CLI)

Do not attempt parallel sub-agent execution. Instead:
1. Convert parallel tasks to sequential steps.
2. Complete each step fully before moving to the next.
3. Record intermediate results in a temporary file if needed.

### When Task Tracking Is Unavailable (Codex CLI, Gemini CLI)

Do not silently omit progress reporting. Instead:
1. Print a status line at the start of each major step: `[STEP N/M] Description`.
2. Print a completion line at the end: `[DONE] Description`.
3. If errors occur, print `[BLOCKED] Reason` and stop.

### When Frontmatter Parsing Is Not Native

To parse `name` and `description` from a SKILL.md:
1. Read the file and locate the first `---` delimiter.
2. Extract text between the first and second `---` as YAML.
3. Parse `name:` and `description:` fields as simple string values.
4. Treat the first line after the second `---` as the skill body start.

---

## Minimum Common Denominator Principle

Design the core skill flow using only these operations, which are available on all platforms:

1. **File read** — read a file and extract content
2. **File edit** — replace a string in a file or write a new file
3. **Shell command** — execute a command and capture output
4. **Text output** — produce text visible to the user

Features that exceed the minimum (e.g., `Skill` tool, `Agent` tool, `TaskCreate`) must be:
1. Used when available on Claude Code.
2. Replaced by a documented fallback on other platforms.
3. Listed explicitly in the skill's **Portability Adapter** section.

If a skill cannot function at all without a platform-specific feature (e.g., it requires sub-agents for correctness), document this limitation in the Portability Adapter section and provide the best-effort sequential alternative.

---

## How to Write the Portability Adapter Section

Every SKILL.md must end with a Portability Adapter section. Use this template:

```markdown
## Portability Adapter

When operating outside Claude Code (e.g. Codex CLI, Gemini CLI):

- **Skill tool:** Not available. Follow the checklist steps manually by reading this file.
- **Agent tool:** Not available. Execute sub-tasks sequentially instead of in parallel.
- **Task tracking:** Not available. Print `[STEP N/M]` status lines to the output instead.
- **File operations:** Use `cat`, `echo`, and shell redirects instead of Read/Write/Edit tools.
- **{Platform-specific feature}:** {fallback description}
```

**Rules for the Portability Adapter section:**
- List every platform-specific tool used in the skill body.
- Provide a concrete fallback for each — not just "not available."
- If the skill degrades in quality without a tool, state this explicitly.
- Do not describe fallbacks for tools that are part of the minimum common denominator (file read, file edit, shell, text output) — these are always available.

---

## Platform Detection

There is no reliable runtime detection of platform. Instead:

- Write skills to work correctly on Claude Code by default.
- Document fallbacks in the Portability Adapter section.
- When a user reports operating on Codex CLI or Gemini CLI, apply the fallback patterns from the Portability Adapter section.
- Do not add `if platform == X` branching inside skill checklists — this increases complexity and reduces readability.

---

## References

- `skeleton-templates/discipline.md` — Portability Adapter placeholder for discipline-type skills
- `skeleton-templates/workflow.md` — Portability Adapter placeholder for workflow-type skills
- `skeleton-templates/technique.md` — Portability Adapter placeholder for technique-type skills
- `skeleton-templates/reference.md` — Portability Adapter placeholder for reference-type skills
