---
name: devlog
description: >
  Generate a detailed session-context devlog entry for the latest commit. Captures what
  changed, why, what was tried, what failed, and lessons learned. Use after committing
  to create a rich record of the development session. Triggers on: "devlog", "log this
  commit", "write a devlog entry", "document what we did", or automatically via the
  post-commit hook.
---

# Devlog Generator

Generate a detailed devlog entry capturing the full session context of the latest commit.

## Process

### Step 1: Gather commit context

Run these commands to understand what was committed:

```bash
git show HEAD --stat
git show HEAD
git log --oneline -5
```

### Step 2: Synthesize session context

From the conversation history in this session, identify:
- **Decisions made:** What choices were considered and why the final approach was chosen
- **Alternatives explored:** Approaches that were tried or discussed but not used
- **Failures encountered:** What didn't work and why
- **Non-obvious insights:** Things learned that weren't expected

### Step 3: Generate the devlog entry

Write a structured entry following this format:

```markdown
## [YYYY-MM-DD] <commit subject line>

**Commit:** `<full sha>` on branch `<branch name>`

### Summary
<1-2 sentence high-level description of what this commit accomplishes>

### Changes
<Bullet list of what changed and WHY each change was made — not just "modified file.py">

### Session Context
<Narrative of the development session: what was the starting point, what approaches were
tried, what problems came up, how they were resolved. This is the valuable part — the
story behind the diff.>

### Lessons Learned
<Non-obvious insights. What would you tell someone about to do similar work? What
surprised you? What convention or pattern emerged?>

---
```

### Step 4: Write to devlog file

- Target file: `docs/devlog.md` in the project root
- If the file doesn't exist, create it with this header:

```markdown
# Development Log

Detailed session-context entries for each commit, newest first.

---

```

- Prepend the new entry after the header (newest first)
- Read the current file contents with the Read tool, then use the Write tool to write the full file with the new entry prepended after the header

### Step 5: Confirm

Tell the user: "Devlog entry added to `docs/devlog.md`" with a brief summary of what was captured.
