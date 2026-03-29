# Automation Catalog

Decision criteria and patterns for each Claude Code automation type. Use this to match
workflow actions to the right automation.

---

## Table of Contents

1. [Hooks](#hooks) — Event-driven automatic actions
2. [Skills](#skills) — Reusable packaged workflows
3. [Agents / Subagents](#agents--subagents) — Autonomous parallel workers
4. [Commands](#commands) — Lightweight slash commands
5. [MCP Servers](#mcp-servers) — External service integrations
6. [Plugins](#plugins) — Bundled collections of the above

---

## Hooks

Hooks fire automatically when specific Claude Code events happen. They're the right choice
when the workflow has a clear trigger-action pattern that should happen every time without
the user thinking about it.

### When to Recommend

- An action should happen automatically after every edit (formatting, linting, type-checking)
- Something should be blocked or validated before it happens (dangerous commands, sensitive files)
- The user wants a notification or side effect on a specific event
- The action is small, fast, and deterministic

### When NOT to Recommend

- The action requires judgment or context (use a skill or agent instead)
- The action is slow (>5 seconds) — hooks block the flow
- The action only applies sometimes (hooks always fire on their event)

### Hook Events

| Event | Fires When | Common Uses |
|-------|-----------|-------------|
| PreToolUse | Before a tool executes | Block dangerous operations, validate inputs |
| PostToolUse | After a tool executes | Auto-format, run linter, run tests |
| Stop | Claude finishes a response | Summary, notification, cleanup |
| SubagentStop | A subagent completes | Aggregate results, trigger follow-up |
| SessionStart | New session begins | Load context, check prerequisites |
| SessionEnd | Session closes | Save state, generate summary |
| UserPromptSubmit | User sends a message | Transform input, inject context |
| Notification | Claude sends a notification | Sound alerts, desktop notifications |

### Common Workflow Patterns

| Workflow Action | Hook Pattern |
|----------------|-------------|
| "I always format after editing" | PostToolUse on Edit/Write → run formatter |
| "I run tests after every change" | PostToolUse on Edit → run related tests |
| "I never want to touch .env files" | PreToolUse on Edit/Write → block if path matches |
| "I want a sound when Claude needs me" | Notification → play audio |
| "I want to lint before committing" | PreToolUse on Bash → intercept git commit, run linter |
| "I load project context at session start" | SessionStart → read state files |
| "I summarize work at end of session" | Stop/SessionEnd → generate summary |

### Hook Types

| Type | Description | Best For |
|------|-------------|----------|
| `command` | Runs a shell command | Formatting, linting, scripts |
| `prompt` | Sends a prompt to Claude for evaluation | Complex validation, context-aware blocking |

Prompt-based hooks are powerful for workflow actions that need judgment — for example,
blocking commands that match a pattern unless certain conditions are met.

---

## Skills

Skills are packaged expertise invoked via `/skill-name` or automatically by Claude. They're
the right choice when a workflow step is a repeatable multi-step process that benefits from
structured guidance.

### When to Recommend

- A workflow has a repeatable process with 3+ steps
- The process benefits from templates, checklists, or reference material
- The user invokes it on demand (slash command) or Claude should apply it automatically
- The process needs bundled scripts or assets

### When NOT to Recommend

- The action is a single command (use a command instead)
- The action should happen automatically without invocation (use a hook instead)
- The action is purely about external data access (use an MCP server instead)

### Invocation Modes

| Mode | Config | Best For |
|------|--------|----------|
| Both (default) | No special config | General-purpose workflows |
| User-only | `disable-model-invocation: true` | Side-effect heavy (deploy, send, publish) |
| Claude-only | `user-invocable: false` | Background knowledge (conventions, patterns) |

### Common Workflow Patterns

| Workflow Action | Skill Pattern |
|----------------|--------------|
| "I review PRs with a checklist" | Skill with bundled checklist, reads diff |
| "I write release notes from commits" | Skill with git context injection via `!command` |
| "I scaffold components from templates" | Skill with bundled templates |
| "I generate API docs for endpoints" | Skill with OpenAPI template |
| "I follow a deploy process" | User-only skill with deploy steps |
| "I have project coding conventions" | Claude-only skill for auto-application |
| "I do the same data transform repeatedly" | Skill with bundled script |

### Skill Anatomy

```
skill-name/
├── SKILL.md           # Instructions (required)
├── scripts/           # Executable code
├── references/        # Docs loaded as needed
├── templates/         # Output templates
└── assets/            # Other bundled files
```

---

## Agents / Subagents

Agents are autonomous Claude instances with their own context and tools. They're the right
choice when work can happen in parallel, needs isolation, or requires specialized focus.

### When to Recommend

- A task can run independently in the background
- Multiple reviews or analyses should happen in parallel
- The task needs focused expertise (security review, performance audit)
- The workflow benefits from a "second pair of eyes"

### When NOT to Recommend

- The task is sequential and depends on prior steps
- The task is simple enough for a hook or skill
- The user needs to interact with the task mid-execution

### Model Selection

| Model | Best For |
|-------|----------|
| haiku | Simple checks, fast scans, repetitive tasks |
| sonnet | Most review/analysis work (recommended default) |
| opus | Complex reasoning, architecture decisions, migrations |

### Common Workflow Patterns

| Workflow Action | Agent Pattern |
|----------------|--------------|
| "I review code for security issues" | security-reviewer agent (read-only) |
| "I want tests written for new code" | test-writer agent |
| "I review PRs from multiple angles" | Parallel agents: code-quality + security + tests |
| "I document APIs as I build them" | api-documenter agent |
| "I want performance checks on changes" | performance-analyzer agent |
| "I need accessibility review on UI" | ui-reviewer agent |
| "I update deps and run tests" | dependency-updater agent (full tool access) |

### Agent Placement

Agents go in `.claude/agents/<name>.md` with frontmatter for model, tools, and description.

---

## Commands

Commands are lightweight slash commands — simpler than skills, no bundled resources. They're
the right choice for one-shot actions that map a short invocation to a specific task.

### When to Recommend

- The action is a single-step or simple multi-step task
- The user wants a quick shortcut (e.g., `/deploy staging`)
- No bundled templates, scripts, or reference material needed
- The action is always user-initiated

### When NOT to Recommend

- The action needs templates or reference files (use a skill)
- The action should happen automatically (use a hook)
- The action is complex enough to need structured guidance (use a skill)

### Common Workflow Patterns

| Workflow Action | Command Pattern |
|----------------|----------------|
| "I deploy to staging frequently" | `/deploy [env]` command |
| "I check CI status often" | `/ci-status` command |
| "I create tickets from the terminal" | `/ticket [title]` command |
| "I sync my branch with main daily" | `/sync` command |

---

## MCP Servers

MCP (Model Context Protocol) servers connect Claude to external tools and data sources.
They're the right choice when the workflow involves interacting with external services.

### When to Recommend

- The workflow involves an external API, database, or service
- The user queries documentation for libraries/frameworks
- The workflow needs browser automation or testing
- The user interacts with project management tools (GitHub, Linear, Jira)
- Cloud infrastructure management is part of the workflow

### When NOT to Recommend

- The action is purely local file manipulation
- The external service is only used once (just use Bash/curl)
- The user has privacy/security concerns about connecting external services

### Common MCP Servers

| Workflow Need | MCP Server |
|--------------|------------|
| Library/framework documentation | context7 |
| Browser testing and automation | Playwright, Puppeteer |
| Database queries and management | Supabase, PostgreSQL, Neon, Turso |
| GitHub issues, PRs, actions | GitHub MCP |
| Project management | Linear, Jira (via Atlassian MCP) |
| Cloud infrastructure | AWS, Cloudflare, Vercel |
| Error tracking | Sentry, Datadog |
| Team communication | Slack, Notion |
| File system access | Filesystem MCP |
| Web search and research | Exa |
| Container management | Docker, Kubernetes |

---

## Plugins

Plugins bundle multiple skills, hooks, agents, and commands into an installable package.
They're the right choice when a workflow needs several automations that work together.

### When to Recommend

- The workflow needs 3+ automations that are logically related
- The user wants to share their setup with a team
- An official plugin already covers the workflow
- The user is building a reusable workflow toolkit

### When NOT to Recommend

- A single skill, hook, or command covers the need
- The workflow is too specific to one project to share
- The user just wants a quick fix, not a package

### Notable Official Plugins

| Workflow Domain | Plugin |
|----------------|--------|
| Plugin and skill development | plugin-dev |
| Git commit workflows | commit-commands |
| Frontend UI design | frontend-design |
| Feature development lifecycle | feature-dev |
| CLAUDE.md management | claude-md-management |
| Code review | superpowers (code-reviewer agent) |
| Full-stack development | fullstack-dev-skills |

### Discovery

Users can browse available plugins with the `/plugin-scout` skill or install directly
with `/plugin install <name>`.
