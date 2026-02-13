---
name: code-surgeon
description: Multi-modal codebase analyzer that routes to Discovery, Review, Optimization, or Implementation Planning based on task classification. Generates surgical prompts, breaking change analysis, and team-convention-aware plans.
---

# code-surgeon

## Overview

**code-surgeon** is a multi-modal orchestrator that transforms requirements into actionable guidance. It routes to the right mode (Discovery, Review, Optimization, or Implementation Planning), performs deep codebase analysis, and generates surgical prompts—precise, file-by-file instructions that guide code changes.

**Core principle:** Match the analysis mode to the task, then deeply understand the codebase, team conventions, and architectural constraints to produce unambiguous implementation guidance.

---

## Task Classification Framework

Before invoking code-surgeon, classify your task using this decision tree:

### Quick Classification

**Do you have a requirement to implement?**
- YES → **Implementation Planning mode** (see "Mode Routing")
- NO → Continue below

**Do you need to understand the codebase first?**
- YES → **Discovery mode** (see "Mode Routing")
- NO → Continue below

**Do you need to assess impact before implementing?**
- YES → **Review mode** (see "Mode Routing")
- NO → Continue below

**Do you need to improve existing code without major changes?**
- YES → **Optimization mode** (see "Mode Routing")
- NO → Ask clarifying questions first

---

## Mode Routing Table

Route your task to the correct mode based on your intent:

| Mode | When to Use | Entry Command | Output |
|------|-----------|---------|--------|
| **Discovery** | "I need to understand this codebase" - Architecture analysis, tech stack assessment, risk identification | `/code-surgeon --mode=discovery` | Audit report with architecture, patterns, risks |
| **Review** | "Will this change break anything?" - Impact assessment, breaking change detection, safety validation | `/code-surgeon "requirement" --mode=review` | Risk report with breaking changes, pre-flight checklist |
| **Optimization** | "How can I improve this code?" - Performance bottlenecks, security vulnerabilities, efficiency gains | `/code-surgeon --mode=optimization` | Optimization report with prioritized recommendations |
| **Implementation Planning** | "I know what I want to build" - Feature implementation, bug fixes, refactoring (DEFAULT) | `/code-surgeon "requirement"` | Implementation plan with surgical prompts (phases, tasks, prompts) |

---

## Hub-and-Spoke Architecture

```
          User Input
               ↓
    ┌─────────────────────┐
    │  Task Classifier    │
    │  (this section)     │
    └──────────┬──────────┘
               ↓
         ┌─────────────────────────────────────┐
         │      Mode Router (select below)      │
         └──────┬──────┬──────┬────────┬───────┘
                ↓      ↓      ↓        ↓
            Discovery Review Optim. Implementation
              Mode    Mode   Mode     Planning
                ↓      ↓      ↓        ↓
         ┌──────────────────────────────────────┐
         │    Shared Analysis Pipeline          │
         │  (Codebase, Patterns, Conventions)   │
         └──────────────────────────────────────┘
                ↓
         ┌──────────────────────────────────────┐
         │    Mode-Specific Output Generator    │
         │  (Report, Plan, Recommendations)     │
         └──────────────────────────────────────┘
                ↓
         Output (varies by mode)
```

---

## Depth Modes (All Modes)

All modes support three depth levels:

### QUICK (5 minutes, ~30K tokens, 85% accuracy)
- **When:** "I'm in a hurry" / small, scoped changes
- **Phase 2 behavior:** Load only Tier 1 (direct) + Tier 2 (dependencies), skip pattern analysis
- **Phase 3 behavior:** 2-4 tasks, reduced analysis
- **Phase 4 behavior:** 5-section prompts instead of 9
- **Output:** Markdown only
- **Cost:** ~$0.04-0.06

### STANDARD (15 minutes, ~60K tokens, 95% accuracy) ← DEFAULT
- **When:** Normal features, bug fixes, most real-world changes
- **Phase 2 behavior:** Load Tier 1 + Tier 2 + Tier 3, extract 3-5 patterns
- **Phase 3 behavior:** Full analysis with 5-8 tasks
- **Phase 4 behavior:** 9-section surgical prompts with framework-specific guidance
- **Output:** All 3 formats (Markdown, JSON, Interactive)
- **Cost:** ~$0.09-0.12

### DEEP (30 minutes, ~90K tokens, 99% accuracy)
- **When:** Architectural changes, risky broad-impact work, high-confidence needed
- **Phase 2 behavior:** Include file history, full dependency graph, 5-10 patterns
- **Phase 3 behavior:** 8-12 tasks, 2-3 alternatives per decision, comprehensive breaking change analysis
- **Phase 4 behavior:** Extended prompts with 10-15 line code examples
- **Output:** All 3 formats with maximum detail
- **Cost:** ~$0.15-0.20

---

## Complete Options Reference

| Option | Type | Required | Default | Purpose |
|--------|------|----------|---------|---------|
| `requirement` | string | Conditional* | — | GitHub issue URL or plain text (required for Implementation Planning, Review modes) |
| `--mode` | discovery\|review\|optimization\|implementation | No | implementation | Which mode to run (see Mode Routing) |
| `--depth` | QUICK\|STANDARD\|DEEP | No | STANDARD | Analysis depth: tradeoff between speed and accuracy |
| `--resume` | session-id | No | — | Resume interrupted session |
| `--format` | markdown\|json\|interactive | No | mode-default | Output format (markdown for humans, json for tools, interactive for step-through) |

*Requirement not needed for Discovery mode (analyzes full codebase) or Optimization mode (analyzes full codebase). Required for Review and Implementation Planning modes.

---

## Entry Points

```bash
# Implementation Planning (default, routes to implementation-planner)
/code-surgeon "Add JWT token refresh to authentication"
/code-surgeon "https://github.com/org/repo/issues/234" --depth=DEEP

# Discovery (routes to discovery-analyzer)
/code-surgeon --mode=discovery
/code-surgeon --mode=discovery --depth=DEEP

# Review (routes to review-analyzer)
/code-surgeon "Update payment processing" --mode=review
/code-surgeon "https://github.com/org/repo/issues/567" --mode=review --depth=STANDARD

# Optimization (routes to optimization-analyzer)
/code-surgeon --mode=optimization
/code-surgeon --mode=optimization --depth=QUICK

# Resume interrupted session (any mode)
/code-surgeon-resume surgeon-20250212-abc123xyz
```

---

## Orchestration Pipeline

code-surgeon is an **orchestrator** that:

1. **Receives** your input (requirement + mode selection)
2. **Classifies** the task against the decision tree
3. **Validates** and checks for PII/secrets
4. **Dispatches** to mode-specific analysis pipeline:
   - **Discovery mode:** Architecture Detector → Pattern Identifier → Risk Analyzer
   - **Review mode:** Impact Analyzer → Breaking Change Detector → Pre-flight Validator
   - **Optimization mode:** Performance Profiler → Security Scanner → Efficiency Analyzer
   - **Implementation Planning mode:** Issue Analyzer + Framework Detector (parallel) → Context Researcher → Implementation Planner → Surgical Prompt Generator
5. **Manages state** across all phases with full resumption support
6. **Returns** outputs (format depends on mode)

---

## Session State Management

code-surgeon persists all work to `.claude/planning/sessions/<session-id>/`:

```
.claude/planning/
├─ sessions/
│  └─ surgeon-20250212-abc123xyz/
│     ├─ state.json              ← Complete session state (resumable)
│     ├─ PLAN.md                 ← Human-readable output
│     ├─ plan.json               ← Machine-readable output
│     ├─ interactive.json        ← CLI step-through data
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

---

## Reference Files (Load on Demand)

Mode-specific workflows and deep content are modularized:

| File | Purpose | When Claude Loads |
|------|---------|-------------------|
| `references/task-classification.md` | Detailed classification framework (5-question flowchart, when to ask clarifying questions) | Before running /code-surgeon (1 time) |
| `references/discovery-mode.md` | Discovery workflow, architecture detection algorithm, pattern identification, tech stack analysis, risk identification, output format, depth mode behavior, examples | When user selects `--mode=discovery` |
| `references/review-mode.md` | Review workflow, risk assessment, breaking change detection, pre-flight validation, code review patterns, output format, depth mode behavior, examples | When user selects `--mode=review` |
| `references/optimization-mode.md` | Optimization workflow, performance detection, security scanning, efficiency analysis, output format, prioritization framework, depth mode behavior, examples | When user selects `--mode=optimization` |

---

## Sub-Skill System Overview

code-surgeon coordinates these specialized sub-skills:

**All Modes (Shared):**
- `/code-surgeon-framework-detector` — Identify tech stack
- `/code-surgeon-context-researcher` — Analyze codebase structure, patterns, dependencies

**Discovery Mode:**
- `/code-surgeon-architecture-detector` — Map system architecture (new)
- `/code-surgeon-pattern-identifier` — Extract architectural patterns (new)
- `/code-surgeon-risk-analyzer` — Identify risks and vulnerabilities (new)

**Review Mode:**
- `/code-surgeon-impact-analyzer` — Assess change impact (new)
- `/code-surgeon-breaking-change-detector` — Detect breaking changes (new)
- `/code-surgeon-preflight-validator` — Pre-flight safety checks (new)

**Optimization Mode:**
- `/code-surgeon-performance-profiler` — Find bottlenecks (new)
- `/code-surgeon-security-scanner` — Scan vulnerabilities (new)
- `/code-surgeon-efficiency-analyzer` — Analyze code efficiency (new)

**Implementation Planning Mode:**
- `/code-surgeon-issue-analyzer` — Parse requirements
- `/code-surgeon-implementation-planner` — Create 6-section plan
- `/code-surgeon-surgical-prompt-generator` — Generate surgical prompts

---

## File Tiering System (Tier 1/2/3)

When analyzing codebases, code-surgeon uses a three-tier file selection system:

**Tier 1 (Direct):** Entry points and main exports
- Public API files, main components, root modules
- Files that define the module's interface
- Examples: `src/index.ts`, `src/main.tsx`, `src/auth.service.ts`

**Tier 2 (Dependencies):** Files imported by Tier 1 files
- Utilities, helpers, shared logic used by main files
- Examples: `src/utils/helpers.ts`, `src/middleware/auth.middleware.ts`

**Tier 3 (Patterns):** Widely-imported files that implement common patterns
- Base classes, shared configurations, widely-used utilities
- Examples: `src/base/BaseModel.ts`, `src/config/constants.ts`

**Why three tiers?** This enables smart token budgeting:
- QUICK mode: Tier 1 only (smallest context)
- STANDARD mode: Tier 1 + Tier 2 (balanced context)
- DEEP mode: All three tiers (exhaustive context)

---

## Error Handling & Recovery

code-surgeon is designed to never lose work.

### What Claude Should Do

**Requirement Validation Fails:**
```
Show error: "Requirement cannot be empty"
Suggest: 'Try: /code-surgeon "describe what you want"'
Action: Stop, don't proceed
```

**Sub-Skill Invocation Fails:**
```
First attempt: Retry once (wait 5 seconds)
If still fails:
  1. Save state.json immediately
  2. Show: "Phase [N] failed. Session saved."
  3. Show session ID: surgeon-20250212-abc123xyz
  4. Suggest: "/code-surgeon-resume surgeon-20250212-abc123xyz"
  5. Stop execution
```

**Token Budget Exceeded:**
```
When approaching 85% of budget:
  - Log warning: "Approaching token limit (12,000/60,000)"
If exceed 100% of budget:
  - Stop loading files
  - Save state.json
  - Show: "Exceeded token budget for [DEPTH] mode"
  - Offer options: (a) reduce depth, (b) resume with QUICK, (c) restart with DEEP
  - Don't proceed without user choice
```

**PII/Secrets Detected:**
```
During validation:
  - BLOCK generation
  - Show: "Cannot generate output: found [TYPE] in code"
  - Show examples: "Found API keys in src/config.ts line 45"
  - Suggest: "Please sanitize code and retry"
  - Action: Stop, don't output plans
```

**User Interrupts (Ctrl+C):**
```
On interrupt signal:
  1. Immediately save state.json
  2. Show: "Session paused and saved"
  3. Show resume: "/code-surgeon-resume surgeon-20250212-abc123xyz"
  4. Exit cleanly
```

---

## Team Guidelines Integration

Create `.claude/team-guidelines.md` to enforce conventions:

```markdown
# Team Guidelines

## Code Style
[language-specific rules]

## Architecture Patterns
[patterns your team uses]

## Security & Compliance
[requirements]
```

code-surgeon automatically:
1. Loads the guidelines file
2. Incorporates rules into all analysis modes
3. Validates outputs against guidelines
4. Flags violations in reports/plans

---

## Framework Support

**35+ frameworks auto-detected** from package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml, etc.

Includes: React, Vue, Angular, Next.js, Django, FastAPI, Express, Rails, Spring Boot, Go, Rust, Python, and more.

Each framework has pattern templates and common mistakes specific to that framework.

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

## Quick Reference

| Command | Purpose |
|---------|---------|
| `/code-surgeon "requirement"` | Implementation Plan (STANDARD depth) |
| `/code-surgeon URL --mode=review` | Review mode (assess impact) |
| `/code-surgeon --mode=discovery` | Discovery mode (analyze codebase) |
| `/code-surgeon --mode=optimization` | Optimization mode (find improvements) |
| `/code-surgeon URL --depth=QUICK` | Quick analysis (5 min, 85% accuracy) |
| `/code-surgeon URL --depth=DEEP` | Thorough analysis (30 min, 99% accuracy) |
| `/code-surgeon-resume <id>` | Resume interrupted session |
| `/code-surgeon-clear-cache` | Clear analysis cache |

---

## Command Syntax Reference

### Plain Text Requirement
```bash
/code-surgeon "Fix the authentication bug"
/code-surgeon "Add dark mode toggle"
```

### GitHub Issue URL
```bash
/code-surgeon "https://github.com/org/repo/issues/123"
```

### Discovery Mode (No Requirement)
```bash
/code-surgeon --mode=discovery
```

### Specify Depth Mode
```bash
/code-surgeon "requirement" --depth=DEEP
/code-surgeon "requirement" --mode=review --depth=STANDARD
```

### Combining Mode and Depth
```bash
/code-surgeon --mode=discovery --depth=DEEP
/code-surgeon "requirement" --mode=optimization --depth=QUICK
```

---

## Getting Started

1. **Classify your task:**
   - Use the Task Classification Framework above
   - Follow the decision tree to pick a mode

2. **Run code-surgeon with the right mode:**
   - Implementation Planning: `/code-surgeon "your requirement"`
   - Discovery: `/code-surgeon --mode=discovery`
   - Review: `/code-surgeon "your requirement" --mode=review`
   - Optimization: `/code-surgeon --mode=optimization`

3. **Review the output:**
   - Check summary, patterns, findings
   - Edit if needed
   - Use for next steps

4. **Optional: Set up team guidelines**
   - Create `.claude/team-guidelines.md`
   - Define your team's conventions
   - code-surgeon will enforce them automatically

---

## Technical Details

**State Management:**
- Session ID format: `surgeon-<date>-<random>`
- State file: `.claude/planning/sessions/<id>/state.json`
- Atomic writes (each phase commits atomically)
- Resumption: Load state, find highest completed phase, continue

**Input Validation:**
- Path traversal checks on repo_root
- PII/secret pattern detection
- Requirement length validation
- Framework version validation

**Error Levels:**
- CRITICAL: Block, pause, require resume
- HIGH: Retry once, then pause
- MEDIUM: Log warning, continue
- LOW: Log info, continue

**Performance (STANDARD depth):**
- Phase 1: 2 min (parallel analysis)
- Phase 2: 5 min (context research)
- Phase 3: 3 min (planning or report generation)
- Phase 4: 2 min (prompts or formatting)
- Phase 5: 1 min (output formatting)
- **Total:** 13 minutes

---

## For Implementation

This skill requires coordinated sub-skills organized by mode. See reference files for mode-specific workflows:
- `references/discovery-mode.md` — Discovery sub-skills
- `references/review-mode.md` — Review sub-skills
- `references/optimization-mode.md` — Optimization sub-skills
- Implementation Planning uses existing sub-skills: issue-analyzer, framework-detector, context-researcher, implementation-planner, surgical-prompt-generator

---

## Next Steps

1. Read `references/task-classification.md` for detailed classification guidance
2. Select the appropriate mode based on your task
3. Load the corresponding reference file for mode-specific workflow
4. Execute `/code-surgeon` with the right parameters
