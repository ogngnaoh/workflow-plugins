---
name: workflow-automation-advisor
description: >
  Recommend Claude Code automations (skills, hooks, agents, commands, MCP servers, plugins)
  tailored to a described workflow or task. Trigger when a user describes a workflow and asks
  what automations would help, says "recommend automations for my workflow", "streamline my
  process", "reduce friction in my workflow", "what hooks or skills should I set up for X",
  or asks how to automate a specific task with Claude Code. Also trigger when someone points
  at a config file (CI workflow, Makefile, package.json scripts) and asks what automations
  would help, describes their daily routine and wants Claude Code to support it, or mentions
  a repetitive multi-step process they want automated — even without saying "automations"
  explicitly. Do NOT trigger when the user wants to analyze a codebase for recommendations
  (that's claude-automation-recommender), wants to create/build a specific automation
  (that's skill-development or hook-development), wants to browse available plugins (that's
  plugin-scout), or is asking to learn how automations work rather than get recommendations.
---

# Workflow Automation Advisor

Analyze a described workflow, task, or pointed-at file and recommend the most impactful
Claude Code automations. This skill is advisory only — it recommends what to build, not
builds it.

## Input Handling

The user's input falls into one of three modes. Detect which and proceed:

**Mode 1 — Clear workflow description.** The user describes a workflow with enough detail to
identify repeated actions, triggers, and outputs. Proceed directly to analysis.

**Mode 2 — File or state pointer.** The user points at a file (path, URL, or config) and
asks what automations would help. Read the file, infer the workflow it supports, then
proceed to analysis.

**Mode 3 — Vague or ambiguous.** The user says something like "help me automate my PR
workflow" or "I do a lot of code reviews." Ask 1-3 targeted clarifying questions before
recommending. Focus on:
- What triggers the workflow (event, schedule, manual action)?
- What are the repeated steps or friction points?
- What does "done" look like?

Stop asking once you can identify at least 3 concrete actions or pain points. Do not
over-interview — if you can reasonably infer, infer.

## Analysis Framework

For the described workflow, evaluate each automation type against the workflow's actions.
Read [references/automation-catalog.md](references/automation-catalog.md) for the full
decision criteria and pattern library for each type.

Walk through the workflow step by step and ask for each action:
1. **Is this triggered by an event?** → Consider a hook
2. **Is this a repeatable multi-step process?** → Consider a skill
3. **Could this run in the background or in parallel?** → Consider an agent
4. **Is this a one-shot action with clear input/output?** → Consider a command
5. **Does this need an external service or data source?** → Consider an MCP server
6. **Are multiple of the above needed together?** → Consider a plugin

Not every workflow needs all types. Recommend only what genuinely reduces friction — a
single well-placed hook beats a multi-component plugin for a narrow workflow.

### Evaluating Impact

Rank each recommendation by:
- **Frequency**: How often does this action happen? (daily > weekly > monthly)
- **Friction**: How painful is it manually? (error-prone > tedious > mildly annoying)
- **Complexity of automation**: How hard is it to set up? (one-liner hook > custom skill > full plugin)

**Priority = Frequency x Friction / Complexity.** High-frequency, high-friction, low-complexity
automations go first.

## Output Format

Return a prioritized list, grouped by priority tier. Use this structure:

```markdown
## Workflow Automation Recommendations

**Workflow**: [one-line summary of the described workflow]

---

### High Priority

#### 1. [Automation Type] — [Short Name]
**Why**: [One sentence — what friction it eliminates]
**How it works**: [2-3 sentences — what it does, when it triggers, what it produces]
**Type**: Hook / Skill / Agent / Command / MCP Server / Plugin

---

### Medium Priority

#### 2. [Automation Type] — [Short Name]
...

---

### Low Priority

#### 3. [Automation Type] — [Short Name]
...

---

**Want deeper recommendations for any category?** Ask for more detail on any specific type.
**Ready to build one of these?** Ask and I can help you implement it.
```

### Output Rules

- Cap at 8-10 recommendations max. If fewer apply, give fewer. Do not pad.
- Be specific to the described workflow — no generic advice.
- If a workflow is better served by something outside Claude Code (a cron job, a CI pipeline,
  a standalone script), say so honestly rather than forcing a Claude Code solution.
- Prefer simple solutions. A single hook beats an elaborate plugin when the workflow is narrow.
- Each recommendation should be self-contained — the user should understand the value without
  reading the others.
