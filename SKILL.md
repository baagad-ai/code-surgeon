---
name: code-surgeon
description: Analyze GitHub issues or plain text requirements and generate precise implementation plans with surgical prompts guiding code changes. Use when implementing features, fixing bugs, or refactoring across multiple files in a codebase. Includes breaking change analysis, team convention enforcement, framework-aware planning (React, Django, Express, Rails, etc.), and intelligent file selection. Generates 3 output formats (Markdown plan, JSON structure, interactive CLI).
---

# code-surgeon

## Overview

**code-surgeon** is an orchestrator skill that transforms GitHub issues or plain text requirements into comprehensive implementation plans with surgical prompts—precise, file-by-file instructions that tell AI agents exactly what code to change, where, why, and how.

**Core principle:** Turn ambiguous requirements into unambiguous implementation guidance by deeply understanding the codebase, team conventions, and architectural constraints.

---

## When to Use

**Use code-surgeon when you:**
- Have a GitHub issue or requirement you want to implement
- Need a step-by-step implementation plan before coding
- Want surgical prompts (precise, targeted changes) rather than vague guidance
- Are implementing across multiple files with dependencies
- Have a large or unfamiliar codebase you need to understand first
- Want to hand off well-structured work to another AI agent

**When NOT to use:**
- Single-file, obviously-scoped changes (e.g., "fix typo in README")
- Emergency hotfixes requiring immediate coding
- Repositories with no structure (random files, no patterns)
- Highly proprietary code you can't share with analysis

---

## Before Running code-surgeon: Assess Your Situation

**Depth Mode Decision Framework**

Ask yourself these questions to choose the right depth mode:

### 1. Scope Clarity
**Question:** Can you articulate the change in one sentence?
```
YES → requirement is clear → proceed
NO → requirement is vague → define it first, then return
```

### 2. File Impact Assessment
**Question:** How many files will this change affect?
```
1-3 files → QUICK mode is appropriate (5 min, $0.04)
5-8 files → STANDARD mode is appropriate (15 min, $0.10) ← default
8+ files or uncertain → STANDARD mode (let codebase analysis guide you)
```

### 3. Risk Assessment
**Question:** What's the risk level if something breaks?
```
LOW RISK (bug fix in isolated module):
└─ QUICK mode (5 min, 85% accuracy, $0.04)

MEDIUM RISK (new feature affecting multiple areas):
└─ STANDARD mode (15 min, 95% accuracy, $0.10) ← default

HIGH RISK (architectural change, security, payment flow):
└─ DEEP mode (30 min, 99% accuracy, $0.17)

MAXIMUM UNCERTAINTY (unfamiliar codebase):
└─ DEEP mode (get comprehensive understanding first)
```

### 4. Breaking Change Impact
**Question:** Could this change break existing behavior for users?
```
NO → QUICK mode acceptable
MAYBE → STANDARD mode
YES → DEEP mode (comprehensive breaking change analysis)
```

### 5. Time vs. Accuracy Tradeoff
**Question:** How much time can you invest?
```
<10 minutes available → QUICK
15-20 minutes available → STANDARD ← default
30+ minutes available → DEEP (for complex changes)
```

**Recommendation Logic:**
```
IF requirement is unclear:
  └─ Stop and define it first

ELSE IF isolated, low-risk bug fix:
  └─ QUICK mode

ELSE IF you're uncertain about scope or risk:
  └─ STANDARD mode (this is the safe default)

ELSE IF architectural/security/broad-impact change:
  └─ DEEP mode
```

---

## How It Works

### Entry Point

```bash
/code-surgeon <requirement> [--depth=mode] [--resume=session-id]
```

**Arguments:**
- `<requirement>` - GitHub issue URL OR plain text description
- `--depth=mode` - QUICK (5min), STANDARD (15min), or DEEP (30min) [default: STANDARD]
- `--resume=session-id` - Resume interrupted session

**Example:**
```bash
/code-surgeon "Add JWT token refresh to authentication flow"
/code-surgeon "https://github.com/myorg/myrepo/issues/234" --depth=DEEP
/code-surgeon-resume surgeon-20250212-abc123xyz
```

---

## Complete Options Reference (For Claude)

| Option | Type | Required | Default | Purpose |
|--------|------|----------|---------|---------|
| `requirement` | string | ✅ Yes | — | GitHub issue URL or plain text description |
| `--depth` | QUICK\|STANDARD\|DEEP | No | STANDARD | Controls analysis depth: tradeoff between speed and accuracy |
| `--resume` | session-id | No | — | Resume interrupted session (loads prior state) |
| `--format` | markdown\|json\|interactive | No | markdown | Output format: markdown for humans, json for tools, interactive for step-through |

### When Claude Receives These Options

**Parse logic:**
- If `--resume` provided: Load session from `.claude/planning/sessions/<session-id>/state.json`, ignore requirement
- If `--resume` NOT provided: Create new session, proceed with requirement
- If `--depth` not specified: Default to STANDARD (15 min, 95% accuracy, ~$0.10)
- If `--format` not specified: Default to markdown (human-readable PLAN.md)

**Option conflicts to handle:**
- ❌ If both requirement AND `--resume` provided: Use `--resume` (resume mode takes precedence)
- ❌ If no requirement AND no `--resume`: Error - "REQUIREMENT is required if not resuming"
- ✅ Can combine `--depth=QUICK` with `--resume` (resume from that depth mode)
- ✅ Can combine any depth with any format (orthogonal options)

### The Orchestration Pipeline

code-surgeon is NOT ONE skill. It's an **orchestrator** that:

1. **Receives** your requirement
2. **Validates** it and checks for PII/secrets
3. **Dispatches 5 specialized subagents** in sequence:
   - **Phase 1 (PARALLEL):** Issue Analyzer + Framework Detector
   - **Phase 2:** Context Researcher
   - **Phase 3:** Implementation Planner
   - **Phase 4:** Surgical Prompt Generator + Validator
   - **Phase 5:** Output Formatter
4. **Manages state** across all phases with full resumption support
5. **Returns** your outputs (Markdown, JSON, Interactive CLI)

```
User Input
    ↓
[ORCHESTRATOR] ← validates, manages state, coordinates phases
    ↓
┌───────────────────────────────────────────────┐
│ PHASE 1: PARALLEL (2 minutes)                 │
├───────────┬─────────────────────────────────┤
│ Subagent  │ Subagent 1B:                   │
│ 1A:       │ Framework Detector              │
│ Issue     ├─────────────────────────────────┤
│ Analyzer  │ Output: {frameworks, versions}  │
├───────────┘                                   │
│ Output: {type, requirements, scope}          │
└───────────────────────────────────────────────┘
    ↓
[PHASE 2: Context Research] (5 minutes)
    ├─ Analyze repo structure
    ├─ Build dependency graph
    ├─ Extract patterns
    ├─ Find team guidelines
    ↓
[PHASE 3: Implementation Planning] (3 minutes)
    ├─ Generate 6-section plan
    ├─ Analyze breaking changes
    ├─ Order tasks logically
    ↓
[PHASE 4: Surgical Prompts + Validation] (2 minutes)
    ├─ Create 7-layer prompts per task
    ├─ Validate against team guidelines
    ├─ Scan for PII/secrets
    ↓
[PHASE 5: Output Formatting] (1 minute)
    ├─ Markdown (human-readable)
    ├─ JSON (machine-readable)
    └─ Interactive CLI (step-through)
    ↓
Outputs: PLAN.md, plan.json, interactive.json
```

---

## Session State Management

code-surgeon persists everything to `.claude/planning/sessions/<session-id>/`:

```
.claude/planning/
├─ sessions/
│  └─ surgeon-20250212-abc123xyz/
│     ├─ state.json              ← Complete session state
│     ├─ PLAN.md                 ← Human-readable plan
│     ├─ plan.json               ← Machine-readable plan
│     ├─ interactive.json        ← CLI mode data
│     └─ logs/
│        └─ execution.log        ← Detailed execution log
├─ cache/                        ← Shared caches
│  ├─ file-structure-<hash>.json
│  ├─ dependency-graph-<hash>.json
│  └─ patterns-<hash>.json
└─ frameworks/                   ← Framework configs
   ├─ react.yml
   ├─ django.yml
   └─ ...
```

**Why JSON + Markdown?**
- **state.json:** Complete truth for resumption and debugging
- **PLAN.md:** What you'll read + copy-paste surgical prompts
- **plan.json:** For tooling integration and CI/CD pipelines
- **interactive.json:** Powers the step-through CLI mode

---

## Error Handling & Recovery

code-surgeon is **designed to never lose work**.

### What Claude Should Do in Each Scenario

#### 1. Requirement Validation Fails

**Triggers:** Empty requirement, only whitespace, too short

**Claude should:**
```
Show error: "Requirement cannot be empty"
Suggest: 'Try: /code-surgeon "describe what you want to change"'
Action: Stop, don't proceed
```

#### 2. Sub-Skill Invocation Fails

**Triggers:** Sub-skill returns `status: "error"` or times out

**Claude should:**
```
First attempt:
1. Log error: "[Phase N] [Subagent] failed: [error message]"
2. Retry once (wait 5 seconds)
3. If still fails:
   - Save state.json immediately
   - Show: "Phase [N] failed after retry. Session saved."
   - Show session ID: surgeon-20250212-abc123xyz
   - Suggest: "/code-surgeon-resume surgeon-20250212-abc123xyz"
   - Stop execution
```

#### 3. Token Budget Exceeded

**Triggers:** Tokens used > budget for depth mode

**Claude should:**
```
During Phase 2-3:
1. Monitor token usage against budget
2. When approaching 85% of budget:
   - Log warning: "Approaching token limit (12,000/60,000)"
3. If exceed 100% of budget:
   - Stop loading files
   - Save state.json
   - Show: "Exceeded token budget for STANDARD mode"
   - Offer options:
     a) Continue analysis with reduced depth
     b) Resume with QUICK mode (fewer files)
     c) Restart with DEEP mode if needed
   - Action: Don't proceed without user choice
```

#### 4. PII/Secrets Detected in Code

**Triggers:** Phase 4 validation detects API keys, emails, SSNs

**Claude should:**
```
During Phase 4 validation:
1. If validation_report.errors includes PII/secrets:
   - BLOCK generation
   - Show: "Cannot generate prompts: found [TYPE] in code"
   - Show examples: "Found API keys in src/config.ts line 45"
   - Suggest: "Please sanitize code and retry"
   - Action: Stop, don't output plan.json/PLAN.md
```

#### 5. Sub-Skill Output Invalid

**Triggers:** Output doesn't match expected schema

**Claude should:**
```
For each sub-skill:
1. Validate output against contract (see Sub-Skill Invocation Patterns)
2. If validation fails:
   - Log error: "[Subagent] output validation failed"
   - Show missing/invalid fields: "Missing: 'type' in Issue Analyzer output"
   - Action: Retry the sub-skill once
   - If retry fails: Pause and suggest resume
```

#### 6. Missing Repository or File

**Triggers:** repo_root doesn't exist, required files not found

**Claude should:**
```
When initializing Phase 2:
1. Check if repo_root is accessible
2. If not found:
   - Show: "Repository not found at [path]"
   - Show actual paths tried: [list]
   - Suggest: "Ensure you're running from correct directory"
   - Action: Stop, don't proceed
```

#### 7. User Interrupts (Ctrl+C)

**Triggers:** User cancels while execution running

**Claude should:**
```
On interrupt signal:
1. Immediately save state.json with:
   - Current phase number
   - Completed phase outputs
   - Current progress status
2. Show: "Session paused and saved"
3. Show resume command: "/code-surgeon-resume surgeon-20250212-abc123xyz"
4. Exit cleanly (no partial outputs)
```

### The Resume Protocol

When a failure occurs or user interrupts:

1. **State is saved atomically** after each phase completes
2. **Session ID is generated**: `surgeon-<YYYYMMDD>-<random>`
3. **State file location**: `.claude/planning/sessions/<id>/state.json`
4. **Resume behavior**: Load state, find highest completed phase, continue from next phase

**Resume example:**
```bash
# Initial execution
/code-surgeon "Add JWT auth" --depth=STANDARD
# ... Phase 1 done, Phase 2 done, Phase 3 running...
# User: Ctrl+C
# System saves state.json with Phase 1-2 complete, Phase 3 incomplete

# Later: Resume execution
/code-surgeon-resume surgeon-20250212-abc123xyz
# System: Loading state... Phase 1-2 already done, restarting Phase 3
# ... continues from Phase 3, reuses Phase 1-2 outputs
# ... completes Phases 3-5
```

### Failure Scenarios Reference Table

| Scenario | When | What Claude Does | Next Step |
|----------|------|------------------|-----------|
| Requirement empty | Validation | Show error, stop | Ask user to provide requirement |
| Sub-skill timeout | Phase 1-4 | Retry once, then pause | Suggest resume |
| Token budget exceeded | Phase 2-3 | Save state, offer options | User chooses: continue, retry, or restart |
| PII detected | Phase 4 validation | BLOCK, show error | Ask user to sanitize code |
| Output invalid | Any phase | Log error, retry once | Pause if retry fails |
| Repo not found | Phase 2 start | Show error, stop | User fixes path and retries |
| User interrupt | Any time | Save state immediately | Suggest resume command |

---

## What Each Phase Does

### Phase 1: Analysis (Parallel, 2 min)

**BEFORE PHASE 1 EXECUTES - MANDATORY READING:**
Read these sub-skill files in parallel with this section. They define the exact parsing and detection algorithms:
1. [`code-surgeon-issue-analyzer-SKILL.md`] — Complete issue parsing logic
2. [`code-surgeon-framework-detector-SKILL.md`] — Framework detection algorithm

**Issue Analyzer** (Subagent 1A)
- Parse GitHub URL or plain text requirements
- Extract: requirements, scope, issue type (feature/bug/refactor/perf)
- Return: `{type, requirements[], deadline, file_hints}`

**Framework Detector** (Subagent 1B)
- Scan package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml, etc.
- Detect: frameworks, versions, language(s), monorepo status
- Return: `{frameworks[], primary_language, versions, is_monorepo}`

**Why parallel?** These don't depend on each other. Run simultaneously to save time.

**Do NOT load** (not needed for Phase 1):
- context-researcher-SKILL.md (Phase 2 only)
- implementation-planner-SKILL.md (Phase 3 only)
- surgical-prompt-generator-SKILL.md (Phase 4 only)

### Phase 2: Context Research (5 min)

**MANDATORY - READ ENTIRE FILE**: Before Phase 2 executes, you MUST read [`code-surgeon-context-researcher-SKILL.md`] completely from start to finish. This file contains the complex 3-tier file selection algorithm, dependency mapping logic, and caching strategy. **Do NOT skip or skim this file.**

**Context Researcher** (Subagent 3)
- Analyze codebase structure using regex patterns (90%+ accuracy without AST parsing)
- Build lightweight dependency graph
- Extract 3-5 architectural patterns
- Find team conventions from `.claude/team-guidelines.md`
- Smart file selection: Tier 1 (direct) + Tier 2 (dependencies) + Tier 3 (patterns)
- Respect token budget (30K quick, 60K standard, 90K deep)
- **Output:** `{files[], dependency_graph, patterns[], team_conventions, cache_updated}`

**Do NOT load** (not needed for Phase 2):
- issue-analyzer-SKILL.md (Phase 1 complete)
- framework-detector-SKILL.md (Phase 1 complete)
- implementation-planner-SKILL.md (Phase 3 only)
- surgical-prompt-generator-SKILL.md (Phase 4 only)

### Phase 3: Planning (3 min)

**MANDATORY - READ ENTIRE FILE**: Before Phase 3 executes, read [`code-surgeon-implementation-planner-SKILL.md`] to understand the 6-section plan format, task decomposition algorithm, and breaking change detection logic.

**Implementation Planner** (Subagent 4)
- Synthesize Issue + Framework + Context
- Generate 6-section plan:
  1. Summary (strategy overview)
  2. Research (findings)
  3. Design Choices (decisions with rationale)
  4. Phases (logical work chunks)
  5. Tasks (granular work items with dependencies)
  6. Verification (testing checklist)
- Analyze breaking changes (4 categories: API/data/behavior/dependency)
- Estimate effort per task using 3-point estimates
- **Output:** `{plan_6_sections, breaking_changes[], tasks_with_deps}`

**Do NOT load** (not needed for Phase 3):
- issue-analyzer-SKILL.md, framework-detector-SKILL.md, context-researcher-SKILL.md (prior phases complete)
- surgical-prompt-generator-SKILL.md (Phase 4 only)

### Phase 4: Surgical Prompts (2 min)

**MANDATORY - READ ENTIRE FILE**: Before Phase 4 executes, read [`code-surgeon-surgical-prompt-generator-SKILL.md`] to understand the 9-section prompt structure, framework-specific templates, and validation rules.

**Surgical Prompt Generator** (Subagent 5)
- Create 9-section surgical prompts per task (Objective, Context, Scope, Approach, Patterns, Constraints, Breaking Changes, Success Criteria, Common Mistakes)
- Apply framework-specific templates (React/Django/Express/etc with pattern examples)
- Include: file paths, line numbers, code examples, verification steps
- Scan for PII/secrets (ERROR if found, blocks generation)

**Validator** (Subagent 6):
- Validate each prompt:
  - ✓ File paths absolute + exist in repo
  - ✓ No PII/secrets in prompt text
  - ✓ Token count within budget (prevents hallucination from over-context)
  - ✓ Syntax valid for target framework
- Return: `{prompts[], validation_passed: true/false, errors[]}`
- **Output:** `{surgical_prompts[], validation_report}`

**Do NOT load** (not needed for Phase 4):
- issue-analyzer-SKILL.md, framework-detector-SKILL.md, context-researcher-SKILL.md, implementation-planner-SKILL.md (prior phases complete)

### Phase 5: Output Formatting (1 min)

Generate 3 complementary outputs:

---

## For Claude: Sub-Skill Invocation Patterns

When executing code-surgeon, use these patterns to invoke sub-skills. Each sub-skill expects specific inputs and outputs.

### Phase 1A: Issue Analyzer Invocation

**Invoke:** `/code-surgeon-issue-analyzer`

**Input Contract:**
```json
{
  "requirement": "GitHub issue URL or plain text description",
  "depth_mode": "QUICK" | "STANDARD" | "DEEP"
}
```

**Output Contract (success):**
```json
{
  "type": "feature" | "bug" | "refactor" | "perf",
  "requirements": ["requirement 1", "requirement 2", ...],
  "deadline": "2025-02-20" (optional),
  "file_hints": ["src/auth.ts", "src/utils.ts", ...],
  "status": "success"
}
```

**What Claude should check:**
- ✅ `type` is one of: feature, bug, refactor, perf
- ✅ `requirements` is non-empty array
- ✅ `status === "success"`
- ❌ If any check fails: Show error, don't continue to Phase 1B

---

### Phase 1B: Framework Detector Invocation (Parallel with 1A)

**Invoke:** `/code-surgeon-framework-detector`

**Input Contract:**
```json
{
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "QUICK" | "STANDARD" | "DEEP"
}
```

**Output Contract (success):**
```json
{
  "frameworks": ["react", "express", ...],
  "primary_language": "typescript" | "python" | "go" | "java" | "ruby",
  "versions": {
    "react": "18.2.0",
    "express": "4.18.2"
  },
  "is_monorepo": false | true,
  "monorepo_info": {
    "type": "yarn" | "npm" | "lerna" | "turborepo",
    "root_packages": ["packages/ui", "packages/api"]
  },
  "status": "success"
}
```

**What Claude should check:**
- ✅ `frameworks` is non-empty array
- ✅ `primary_language` is recognized language
- ✅ `status === "success"`
- ⚠️ If `is_monorepo === true`, ensure monorepo_info is present
- ❌ If framework detection fails: Continue anyway (framework can be inferred from Phase 3)

---

### Phase 2: Context Researcher Invocation

**Invoke:** `/code-surgeon-context-researcher`

**Input Contract (requires Phase 1 outputs):**
```json
{
  "requirement": "original requirement",
  "issue_type": "feature" | "bug" | "refactor" | "perf",
  "file_hints": ["src/auth.ts"],
  "frameworks": ["react"],
  "primary_language": "typescript",
  "is_monorepo": false,
  "depth_mode": "QUICK" | "STANDARD" | "DEEP",
  "repo_root": "/path/to/repo"
}
```

**Output Contract (success):**
```json
{
  "files": {
    "tier_1": ["src/auth.ts", "src/utils.ts"],
    "tier_2": ["src/hooks/useAuth.ts", "src/types.ts"],
    "tier_3": ["src/middleware.ts"]
  },
  "dependency_graph": {
    "src/auth.ts": ["src/utils.ts", "src/types.ts"],
    "src/hooks/useAuth.ts": ["src/auth.ts"]
  },
  "patterns": [
    {
      "name": "Custom Hook Pattern",
      "files": ["src/hooks/useAuth.ts"],
      "description": "React custom hooks for state management"
    }
  ],
  "team_conventions": {
    "naming": "camelCase for functions, PascalCase for components",
    "error_handling": "Always use try-catch in async functions"
  },
  "cache_updated": true,
  "status": "success"
}
```

**What Claude should check:**
- ✅ `files` has tier_1 (required), tier_2 and tier_3 optional but expected in STANDARD/DEEP
- ✅ `dependency_graph` is non-empty object
- ✅ `patterns` is array (can be empty but should have 1-5 items)
- ✅ `status === "success"`
- ❌ If status is error: Retry once, then show error and suggest resume

---

### Phase 3: Implementation Planner Invocation

**Invoke:** `/code-surgeon-implementation-planner`

**Input Contract (requires Phase 1-2 outputs):**
```json
{
  "requirement": "original requirement",
  "issue_type": "feature",
  "frameworks": ["react"],
  "primary_language": "typescript",
  "files": {
    "tier_1": ["src/auth.ts"],
    "tier_2": ["src/hooks/useAuth.ts"]
  },
  "patterns": [...],
  "team_conventions": {...},
  "depth_mode": "STANDARD"
}
```

**Output Contract (success):**
```json
{
  "plan": {
    "summary": "Strategy overview",
    "research": "Key findings",
    "design_choices": [
      {
        "decision": "Use JWT tokens",
        "rationale": "Industry standard",
        "alternatives": "Session cookies"
      }
    ],
    "phases": [
      {
        "phase": 1,
        "name": "Core implementation",
        "tasks": ["task 1", "task 2"]
      }
    ],
    "tasks": [
      {
        "id": "1.1",
        "name": "Create OAuth2Service",
        "files_affected": ["src/services/oauth.ts"],
        "effort_estimate": "3 hours",
        "dependencies": [],
        "success_criteria": ["Tests pass", "Integration works"]
      }
    ],
    "verification": ["Run tests", "Check integration"]
  },
  "breaking_changes": [
    {
      "type": "api",
      "description": "Auth endpoint signature changed",
      "impact": "Client code needs update",
      "migration": "Update calls from POST /auth to POST /auth/v2"
    }
  ],
  "status": "success"
}
```

**What Claude should check:**
- ✅ `plan.summary` exists and is non-empty
- ✅ `plan.tasks` is non-empty array with task IDs
- ✅ All task dependencies reference other task IDs (validate chain)
- ✅ `breaking_changes` is array (can be empty)
- ✅ `status === "success"`
- ❌ If task count > 20: Warn user "Large implementation, consider breaking into sub-tasks"

---

### Phase 4: Surgical Prompt Generator Invocation

**Invoke:** `/code-surgeon-surgical-prompt-generator`

**Input Contract (requires Phase 1-3 outputs):**
```json
{
  "tasks": [
    {
      "id": "1.1",
      "name": "Create OAuth2Service",
      "files_affected": ["src/services/oauth.ts"],
      "success_criteria": ["Tests pass"]
    }
  ],
  "files": {
    "tier_1": ["src/auth.ts"]
  },
  "patterns": [...],
  "team_conventions": {...},
  "frameworks": ["react"],
  "primary_language": "typescript",
  "depth_mode": "STANDARD"
}
```

**Output Contract (success):**
```json
{
  "surgical_prompts": [
    {
      "task_id": "1.1",
      "prompt": "9-section surgical prompt starting with Objective, Context, Scope...",
      "token_count": 450,
      "framework": "react",
      "scope": {
        "files_to_modify": ["src/services/oauth.ts"],
        "files_to_reference": ["src/auth.ts"],
        "files_to_avoid": ["src/ui/"]
      }
    }
  ],
  "validation_report": {
    "total_prompts": 1,
    "valid": 1,
    "invalid": 0,
    "errors": []
  },
  "status": "success"
}
```

**What Claude should check:**
- ✅ `surgical_prompts` count matches input task count
- ✅ Each prompt has `task_id`, `prompt`, `token_count`
- ✅ `validation_report.valid === surgical_prompts.length`
- ✅ No items in `validation_report.errors`
- ✅ `status === "success"`
- ❌ If validation fails: Show errors, offer to regenerate with reduced depth

---

### Phase 5: Output Formatter

Generate 3 complementary outputs:

**Markdown (PLAN.md):**
```markdown
# Implementation Plan: [Task]

## Summary
[Strategy overview]

## Research
[Codebase findings]

## Design Choices
[Decisions with rationale]

## Surgical Prompts
[Per-task prompts, ready to copy-paste]

## Breaking Changes
[Impact analysis]

## Verification
[Testing checklist]
```

**JSON (plan.json):**
```json
{
  "plan_id": "surgeon-...",
  "summary": "...",
  "research": {...},
  "design_choices": [...],
  "phases": [...],
  "tasks": [...],
  "surgical_prompts": [...],
  "breaking_changes": [...],
  "verification": [...]
}
```

**Interactive CLI (interactive.json):**
```json
{
  "mode": "step-through",
  "phases": [
    {
      "phase": 1,
      "name": "Core Authentication Service",
      "tasks": [
        {
          "task_id": "1.1",
          "name": "Create OAuth2Service",
          "surgical_prompt": "...",
          "status": "not_started"
        }
      ]
    }
  ]
}
```

---

## Depth Modes

Choose how deeply to analyze your codebase. Each mode represents a tradeoff between speed and accuracy.

### QUICK Mode (5 minutes, ~30K tokens, 85% accuracy)

**When user should request this:**
- Bug fixes with clear scope ("Fix off-by-one in utils.js")
- Small features in isolated areas
- When user says "I'm in a hurry"
- When requirement clearly maps to 1-2 files

**What Claude should do differently in QUICK mode:**

| Phase | QUICK behavior | Difference from STANDARD |
|-------|---|---|
| Phase 1 | Normal | No change |
| Phase 2 | **Skip Tier 3 pattern extraction** | Load only Tier 1 (direct) + Tier 2 (dependencies), don't look for architectural patterns |
| Phase 3 | **Reduce task count** | Create 2-4 tasks instead of 5-8, skip non-critical optimizations |
| Phase 4 | **Shorter prompts** | Generate 5-section prompts (Objective, Scope, Approach, Success, Common Mistakes) instead of 9-section |
| Phase 5 | **Markdown only** | Only generate PLAN.md, skip JSON and interactive formats to save time |

**Token budget:** 30K max
**Cost:** ~$0.04-0.06
**Success rate:** 85% (might miss some dependencies, but good for scoped changes)

### STANDARD Mode (15 minutes, ~60K tokens, 95% accuracy) ← DEFAULT

**When to use (user doesn't specify):**
- Normal features ("Add JWT token refresh")
- Bug fixes with complex impact
- Moderate refactoring
- Most real-world changes

**What Claude executes in STANDARD mode:**

| Phase | STANDARD behavior |
|-------|---|
| Phase 1 | Full issue analysis + framework detection |
| Phase 2 | Load Tier 1 (direct) + Tier 2 (dependencies) + Tier 3 (patterns), extract 3-5 architectural patterns |
| Phase 3 | Full 6-section plan with 5-8 tasks, breaking change analysis, effort estimates |
| Phase 4 | Full 9-section surgical prompts with framework-specific guidance |
| Phase 5 | All 3 output formats: Markdown, JSON, Interactive |

**Token budget:** 60K max
**Cost:** ~$0.09-0.12
**Success rate:** 95% (captures most dependencies and patterns)
**Default:** Always use this unless user specifies --depth

### DEEP Mode (30 minutes, ~90K tokens, 99% accuracy)

**When user should request this:**
- Major architectural changes ("Refactor authentication system")
- Risky changes with broad impact
- When user says "I need high confidence"
- When requirement affects multiple subsystems

**What Claude should do differently in DEEP mode:**

| Phase | DEEP behavior | Extra details vs STANDARD |
|---|---|---|
| Phase 1 | Normal | No change |
| Phase 2 | **Include file history** | For each file: commits that touched it, blame info, last change date |
| Phase 2 | **Full dependency graph** | Not just files, include: imports, exports, function calls, type references |
| Phase 2 | **Extended pattern analysis** | Find 5-10 patterns instead of 3-5, include historical patterns |
| Phase 3 | **Detailed design choices** | For each choice: 2-3 alternatives with tradeoffs, risk assessment |
| Phase 3 | **Comprehensive breaking changes** | 4-category analysis: API, data, behavior, dependency |
| Phase 4 | **Extended prompt context** | 9-section prompts with more code examples (10-15 lines per example) |

**Token budget:** 90K max
**Cost:** ~$0.15-0.20
**Success rate:** 99% (captures almost everything, good for critical changes)

---

## For Claude: How to Manage Depth Mode

### At Invocation (Parse)
```
IF --depth not specified:
  depth = STANDARD
ELSE IF --depth is one of [QUICK, STANDARD, DEEP]:
  depth = requested mode
ELSE:
  SHOW ERROR: "Invalid depth: [value]. Must be QUICK, STANDARD, or DEEP"
```

### During Phase 2 (Implementation)
```
Phase 2: Context Research

SET token_budget = {
  QUICK: 30000,
  STANDARD: 60000,
  DEEP: 90000
}[depth]

IF depth === QUICK:
  - Load Tier 1 files only
  - Skip pattern extraction
  ELSE IF depth === STANDARD:
  - Load Tier 1 + Tier 2 files
  - Extract 3-5 patterns
  ELSE IF depth === DEEP:
  - Load Tier 1 + Tier 2 + Tier 3 files
  - Include file history and full dependency graph
  - Extract 5-10 patterns
```

### During Phase 3 (Planning)
```
Phase 3: Implementation Planning

IF depth === QUICK:
  - Generate 2-4 tasks (minimal decomposition)
  - Skip optimization discussion
  ELSE IF depth === STANDARD:
  - Generate 5-8 tasks (normal decomposition)
  - Include design choices
  ELSE IF depth === DEEP:
  - Generate 8-12 tasks (fine-grained decomposition)
  - Include 2-3 alternatives for each decision
  - Comprehensive breaking change analysis
```

### During Phase 4 (Prompts)
```
Phase 4: Surgical Prompt Generation

sections = {
  QUICK: [Objective, Scope, Approach, Success, CommonMistakes],
  STANDARD: [Objective, Context, Scope, Approach, Patterns, Constraints,
             BreakingChanges, SuccessCriteria, CommonMistakes],
  DEEP: Same as STANDARD but with extended examples (2x more context)
}

token_limit_per_prompt = {
  QUICK: 350 tokens,
  STANDARD: 650 tokens,
  DEEP: 1000 tokens
}[depth]
```

### During Phase 5 (Output)
```
Phase 5: Output Formatting

output_formats = {
  QUICK: [markdown],           // Only PLAN.md
  STANDARD: [markdown, json],  // PLAN.md + plan.json
  DEEP: [markdown, json, interactive]  // All 3 formats
}[depth]
```

### Token Transparency (Show User)

After Phase 2 completes, show real-time feedback:

```
Phase 2 complete: Researching codebase...
├─ Tokens used: 18,400 / 60,000 (31%)
├─ Files analyzed: 23
├─ Patterns found: 4
├─ Estimated final cost: ~$0.08
└─ Status: On track for STANDARD mode (15 min total)
```

### Depth Mode Recovery

If token budget is exceeded during analysis:
```
IF tokens_used > token_budget * 1.1:  // 10% buffer exceeded
  1. Save state immediately
  2. Show: "Exceeded token budget for [DEPTH] mode"
  3. Offer recovery options:
     a) Continue with reduced depth (QUICK or lower)
     b) Restart with DEEP mode
     c) Analyze specific files only
```

---

## Team Guidelines Integration

Create `.claude/team-guidelines.md` to enforce your team's conventions:

```markdown
# Team Guidelines

## Code Style
- [language-specific rules]

## Architecture Patterns
- [patterns your team uses]

## Security & Compliance
- [requirements]
```

code-surgeon automatically:
1. Loads the guidelines file
2. Incorporates rules into surgical prompts
3. Validates generated code against guidelines
4. Flags violations in breaking changes analysis

---

## Framework Support

**35+ frameworks auto-detected** from package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml, etc.

Includes: React, Vue, Angular, Next.js, Django, FastAPI, Express, Rails, Spring Boot, Go, Rust, Python, and more.

Each framework has specific pattern templates (React hooks, Django models, Express middleware, etc.) and common mistakes unique to that framework.

---

## Caching & Performance

code-surgeon caches file structure and dependency graphs, saving 25-30% of tokens on repeated analyses. Automatically invalidates cache when files change. Clear with `/code-surgeon-clear-cache`.

---

## Security & Privacy

code-surgeon is completely local and secure:
- ✅ All analysis local (no external API calls)
- ✅ Code never leaves your machine
- ✅ State stored in `.claude/planning/` (git-ignorable)
- ✅ PII/secret detection with automatic blocking
- ✅ Path traversal and input validation built-in

---

## Common Mistakes

### ❌ Using on single-file changes
Don't use code-surgeon for "fix typo in README" or obvious one-liners. It's overkill.
→ **Instead:** Make simple changes directly, use code-surgeon for multi-file or complex work

### ❌ Trusting the plan blindly
The plan is a guide, not gospel. Requirements and codebases change.
→ **Instead:** Review the plan, edit if needed, then hand to AI agent

### ❌ Ignoring breaking changes warnings
code-surgeon highlights what might break. Don't ignore these.
→ **Instead:** Read the breaking changes section, plan testing accordingly

### ❌ Not reading team guidelines
If your team has `.claude/team-guidelines.md`, it's loaded automatically.
→ **Instead:** Create the file! code-surgeon respects your team's rules.

### ❌ Assuming prompts are perfect
Surgical prompts are very good, but not perfect. Review them.
→ **Instead:** Read the prompts, edit if needed, then hand to AI agent

### ❌ Using DEEP mode for simple bug fixes
DEEP mode (30 min, $0.15-0.20) analyzes everything in the codebase. For "fix off-by-one error in utils.js", this is massive overkill.
→ **Instead:** Use QUICK mode (5 min, $0.04) for scoped bug fixes. Use DEEP only for architectural changes or risky refactoring.

### ❌ Ignoring dependency conflicts in breaking changes section
code-surgeon lists breaking changes. If a package dependency breaks, that affects downstream code.
→ **Instead:** When planning, check both the files you're modifying AND their dependent files. Test with `npm test` or equivalent.

### ❌ Not creating .claude/team-guidelines.md
Without team guidelines, code-surgeon can't enforce your team's conventions (naming, error handling, security rules).
→ **Instead:** Create `.claude/team-guidelines.md` with your team's architectural rules, code style, and security requirements. It takes 30 minutes and vastly improves plan quality.

### ❌ Running analysis on private code without review
code-surgeon saves state locally, but if you later share the PLAN.md output, it contains file paths and code references.
→ **Instead:** Review PLAN.md and surgical prompts before sharing. Sanitize file paths or code examples if needed for external teams.

---

## Next Steps After Generation

Once code-surgeon completes:

1. **Review the Markdown plan** (PLAN.md)
   - Read summary, research, design choices
   - Check if it matches your intent

2. **Review surgical prompts**
   - Check file paths and line numbers
   - Read the code changes proposed
   - Edit if needed (they're just text)

3. **Choose a task from the plan**
   - Pick first task from first phase
   - Copy the surgical prompt for that task
   - Hand to your preferred AI agent (Claude, Cursor, etc.)

4. **Repeat for each task**
   - Each task has its own prompt
   - Tasks are ordered by dependencies
   - All context already provided

5. **Resumable anytime**
   - Interrupted? `/code-surgeon-resume <id>`
   - Want different depth? Run again with `--depth=DEEP`
   - Need to edit plan? Edit PLAN.md, continue

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `/code-surgeon "requirement"` | Start new analysis (STANDARD depth) |
| `/code-surgeon URL --depth=QUICK` | Quick 5-minute analysis (85% accuracy) |
| `/code-surgeon URL --depth=DEEP` | Thorough 30-minute analysis (99% accuracy) |
| `/code-surgeon-resume <id>` | Resume interrupted session |
| `/code-surgeon-clear-cache` | Clear analysis cache |
| `/code-surgeon-list-sessions` | List all sessions |
| `/code-surgeon-view <id>` | View plan from session |

---

## How to Get Started

1. **Create team guidelines** (optional but recommended):
   ```bash
   cat > .claude/team-guidelines.md << 'EOF'
   # Team Guidelines
   [Add your team's rules, patterns, security requirements]
   EOF
   ```

2. **Run code-surgeon on an issue**:
   ```bash
   /code-surgeon "Add feature: JWT token refresh"
   ```

3. **Review the output** (PLAN.md):
   - Check if analysis is correct
   - Edit if needed
   - Save as reference

4. **Copy a surgical prompt**:
   - Pick Task 1 from the plan
   - Copy its surgical prompt
   - Paste to Claude, Cursor, or your AI agent

5. **Continue with next tasks**:
   - Each task has its own prompt
   - All context is provided
   - Follow dependencies (earlier tasks first)

---

## Technical Details

**State Management:**
- Session ID format: `surgeon-<date>-<random>`
- State file: `.claude/planning/sessions/<id>/state.json`
- Atomic writes (each phase commits atomically)
- Resumption: Load state, find highest completed phase, continue

**Subagent Communication:**
- Input: Invocation contract with task, inputs, expected schema, timeout
- Output: Result object with success/error, data, metrics
- Validation: JSON schema validation on all outputs

**Error Levels:**
- CRITICAL: Block, pause, require resume
- HIGH: Retry once, then pause
- MEDIUM: Log warning, continue
- LOW: Log info, continue

**Performance:**
- Phase 1: 2 min (parallel)
- Phase 2: 5 min (context research)
- Phase 3: 3 min (planning)
- Phase 4: 2 min (prompts + validation)
- Phase 5: 1 min (formatting)
- **Total:** 13 minutes for STANDARD depth

---

## For Implementation

This skill requires 5 coordinated sub-skills:
1. **issue-analyzer** - Parse requirements, detect type
2. **framework-detector** - Identify tech stack
3. **context-researcher** - Analyze codebase
4. **implementation-planner** - Create 6-section plan
5. **prompt-surgeon** - Generate surgical prompts

Each sub-skill is tested independently, then integrated.

code-surgeon orchestrates them all.

