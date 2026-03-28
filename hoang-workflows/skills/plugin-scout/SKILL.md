---
name: plugin-scout
description: Browse all configured Claude Code plugin marketplaces and recommend which plugins to install based on the user's workflow, project, or task description. Use when the user asks "what plugins should I use", "what's available in the marketplace", "find me plugins for X", "are there plugins that would help with this project", or wants to audit their setup for missing plugins. Triggers on plugin discovery, plugin browsing, plugin recommendations, marketplace exploration, and "which plugins" questions. Also triggers when the user points at a codebase or CLAUDE.md and asks what plugins would help. Do NOT use for: installing a specific named plugin (that's /plugin install), configuring or troubleshooting an already-installed plugin, creating new skills or plugins, or tasks that merely mention a plugin by name without asking for recommendations.
---

# Plugin Scout

Scan all configured Claude Code marketplaces and recommend plugins that match the user's workflow. This skill is **read-only** — it presents recommendations but never installs plugins or modifies configuration.

## How It Works

You act as a coordinator that spawns parallel scanner agents — one per marketplace — to search for relevant plugins. Each agent performs an adaptive-depth scan: fast registry skim first, deeper inspection only for strong candidates. You merge the results and present a unified ranked list.

## Step 1: Determine the Workflow Profile

The user's needs come from one of three sources. Detect which applies:

**Description-only:** The user described a task or workflow in conversation. Extract key themes: domain (data engineering, frontend, DevOps, etc.), tools/frameworks mentioned, and the nature of the work (building, debugging, migrating, etc.).

**Codebase-only:** The user pointed at a codebase or asked "what plugins would help here?" Read the project's `CLAUDE.md` file (check both the working directory and any path the user specified). CLAUDE.md is the sole codebase signal — do not parse package.json, directory structure, or other files.

**Both:** The user described something AND has a codebase. Read CLAUDE.md and combine it with the verbal description.

If CLAUDE.md doesn't exist (codebase mode), tell the user: "I don't see a CLAUDE.md in this project. Can you describe what you're working on instead?"

If CLAUDE.md exists but is too vague to extract a workflow, say: "Your CLAUDE.md doesn't give me enough to work with — can you describe what you're working on?"

### Clarification Gate

Before spawning scanner agents, assess whether you have enough signal. If the description is broad enough that it would match half the catalog (e.g., "software engineering", "coding"), ask 1-2 narrowing questions:

- "What kind of work are you doing?" (e.g., data pipelines, web app, mobile, infrastructure)
- "Any specific tools or frameworks involved?" (e.g., dbt, React, Terraform, Spark)

Only ask what's needed. If the user said "I'm building a dbt pipeline on Snowflake," you have enough — don't ask more.

### Build the Profile

Synthesize a **workflow profile** — a 1-3 sentence summary capturing:
- Domain/industry context
- Key tools, languages, and frameworks
- Nature of the work (building from scratch, migrating, debugging, data processing, etc.)

Example: *"Biomedical data lake project using Python/pandas. Building ETL pipelines that extract sample metadata from S3-hosted vendor files (Excel, CSV, FASTQ) across 6 modalities, with a linkage engine that maps parsed samples to a master sample registry."*

## Step 2: Discover Marketplaces

Read `~/.claude/plugins/known_marketplaces.json` to get the list of all configured marketplaces. For each one, resolve its directory path at `~/.claude/plugins/marketplaces/<marketplace-name>/`.

Verify each marketplace directory exists and contains a `.claude-plugin/marketplace.json` file. If a marketplace is listed but its directory is missing or has no registry, skip it — you'll note it at the end.

## Step 3: Spawn Scanner Agents

Spawn one Agent per marketplace, **all in parallel** in a single message. Each agent gets this prompt:

---

**Scanner Agent Prompt Template:**

```
You are scanning a plugin marketplace to find plugins relevant to a user's workflow.

WORKFLOW PROFILE:
{workflow_profile}

MARKETPLACE: {marketplace_name}
REGISTRY PATH: {path_to_marketplace.json}
PLUGINS DIRECTORY: {path_to_marketplace}/plugins/

YOUR TASK — Adaptive-depth scan:

LEVEL 1 — Registry skim:
Read the marketplace.json file at the registry path. It contains a list of all plugins with their names and descriptions. For each plugin, assess whether its description suggests relevance to the workflow profile above.

LEVEL 2 — Metadata drill:
For the top 5-10 candidates from Level 1, navigate to their directory under the plugins directory and read their .claude-plugin/plugin.json file. This gives you richer metadata: skill lists, version, compatibility. Use this to filter further.

LEVEL 3 — Skill inspection:
For the top 3-5 candidates from Level 2, read the SKILL.md files inside their skills/ subdirectories. This tells you exactly what each skill does. Use this to write precise rationale.

RETURN your results as a JSON array (and nothing else) with this structure:
[
  {
    "plugin_name": "the-plugin-name",
    "marketplace": "{marketplace_name}",
    "description": "One-line description from the plugin",
    "relevant_skills": ["skill-a", "skill-b"],
    "rationale": "1-2 sentences explaining why this plugin fits the workflow",
    "confidence": "high | medium | low"
  }
]

Include only plugins with genuine relevance. Return an empty array [] if nothing matches. Do not force matches — an empty result is a valid and useful signal.
```

---

Use `subagent_type: "Explore"` for the scanner agents since they are doing read-only codebase exploration.

## Step 4: Merge and Rank

Once all scanner agents return:

1. **Collect** all shortlists into a single list
2. **Deduplicate** — if the same plugin appears from multiple marketplaces, keep one entry and note both sources
3. **Rank** by confidence tier: high first, then medium, then low. Within the same tier, plugins with more relevant skills rank higher
4. **Drop** any entries where the rationale feels like a stretch after seeing all results in context

## Step 5: Present Recommendations

Format the merged list as markdown:

```
## Plugin Recommendations for: [short workflow summary]

### 1. plugin-name (marketplace-name)
**Skills:** skill-a, skill-b, skill-c
**Why:** 1-2 sentence rationale.
**Confidence:** High

### 2. another-plugin (marketplace-name)
**Skills:** skill-x
**Why:** Rationale here.
**Confidence:** Medium
```

No fixed cap on list length — show as many as are genuinely relevant.

If any marketplaces were skipped (missing directory, no registry, agent failure), add a note at the bottom:
> Note: Marketplace `X` was configured but unavailable during this scan.

If zero plugins matched across all marketplaces:
> I scanned [N] plugins across [M] marketplaces and didn't find strong matches for your workflow. This could mean your needs are covered by general-purpose tools, or a custom skill might be warranted.

## What This Skill Does NOT Do

- Install plugins or offer to install them
- Modify settings.json, CLAUDE.md, or any configuration
- Recommend MCP servers, hooks, or non-plugin automations (use `claude-automation-recommender` for that)
- Look outside configured marketplaces
- Parse package.json, directory structure, or anything besides CLAUDE.md for codebase signals
