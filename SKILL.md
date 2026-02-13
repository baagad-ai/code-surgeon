---
name: code-surgeon
description: Analyze, plan, review, and optimize any codebase across 4 modes: Discovery (understand architecture and risks), Review (validate changes and detect breaking changes), Optimization (find bottlenecks and vulnerabilities), Implementation Planning (generate step-by-step guidance). Works with React, Django, Rails, Go, Rust, and 30+ frameworks. Use when analyzing codebase structure, assessing feature safety, finding security issues, planning implementations, or discovering performance problems.
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

## Discovery Mode Orchestration

Discovery mode performs deep codebase analysis to generate an **Audit Report** without requiring a specific change or requirement. This section details the exact 6-phase orchestration workflow.

### Executive Summary

**Duration:** 17 minutes (STANDARD) | **Token Budget:** 60K | **Accuracy:** 95%

Discovery mode routes through 5 core sub-skills in strict sequence to understand architecture, patterns, and risks:

```
Framework Detection (2 min)
    ↓
Context Research (5 min)
    ↓
Architecture Detection (3 min)
    ↓
Pattern Identification (3 min)
    ↓
Tech Stack Analysis (2 min)
    ↓
Risk Identification (2 min)
    ↓
Audit Report (Generated Markdown)
```

### Phase 1: Framework Detection (2 minutes)

**Sub-skill:** `/code-surgeon-framework-detector`

**Purpose:** Detect tech stack, programming languages, frameworks, versions, and monorepo structure.

**Input Contract:**
```json
{
  "repo_root": "/absolute/path/to/repo",
  "timeout_ms": 120000
}
```

**Field Context:**
- `repo_root`: User-provided (absolute path to repository)
- `timeout_ms`: Global default (2 minutes for Phase 1)

**Output Contract (Success):**
```json
{
  "primary_language": "typescript",
  "primary_framework": "React",
  "frameworks": [
    {
      "name": "React",
      "version": "18.2.0",
      "language": "typescript",
      "category": "frontend"
    },
    {
      "name": "Express",
      "version": "4.18.2",
      "language": "typescript",
      "category": "backend"
    }
  ],
  "languages": [
    {"language": "typescript", "file_count": 145, "percentage": 85},
    {"language": "javascript", "file_count": 25, "percentage": 15}
  ],
  "is_monorepo": false,
  "has_typescript": true,
  "has_testing": true,
  "has_documentation": true,
  "confidence": 0.96
}
```

**Error Handling:**
- If repo not found: Stop immediately, return "Repository not found"
- If unreadable: Stop immediately, return "Repository access denied"
- If timeout: Return partial results with low confidence flag

**Token Cost:** ~1K tokens

---

### Phase 2: Context Research (5 minutes)

**Sub-skill:** `/code-surgeon-context-researcher`

**Purpose:** Analyze codebase structure, build dependency graph, identify structural patterns, find team conventions.

**Note:** This phase identifies **structural patterns** (code organization, naming conventions, folder structure patterns). Deep architectural and design patterns are identified in Phase 4.

**Input Contract:**
```json
{
  "issue_type": "architecture",
  "requirements": ["Understand full codebase"],
  "primary_language": "typescript",
  "frameworks": [...],  // from Phase 1
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 300
}
```

**Field Context:**
- `primary_language`, `frameworks`: From Phase 1 output (previous phase)
- `repo_root`: User-provided (same as Phase 1)
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)
- `timeout_seconds`: Global default (5 minutes for Phase 2)

**Output Contract (Success):**
```json
{
  "files_selected": [
    {
      "path": "src/auth.ts",
      "tier": 1,
      "size_bytes": 2400,
      "relevance": "critical",
      "reason": "Core authentication module"
    }
  ],
  "file_count": {
    "tier_1": 8,
    "tier_2": 25,
    "tier_3": 12,
    "total": 45
  },
  "dependency_graph": {
    "src/auth.ts": {
      "imports": ["src/utils.ts"],
      "imported_by": ["src/api.ts"],
      "impact": "critical"
    }
  },
  "structural_patterns": [
    {
      "name": "Custom Hook Pattern",
      "example_file": "src/hooks/useAuth.ts",
      "description": "Custom hooks located in src/hooks/ directory for state management",
      "location": "src/hooks/**/*.ts"
    }
  ],
  "team_conventions": [
    "camelCase for functions, PascalCase for components",
    "Always use try-catch in async functions"
  ]
}
```

**Error Handling:**
- If timeout: Return partial files found (Tier 1 only), mark as incomplete
- If token budget exceeded: Remove Tier 3 patterns, retry with Tier 1-2 only
- If no files found: Stop, return "Repository structure unreadable"

**Token Cost:** 30K-90K tokens (varies by depth mode)

---

### Phase 3: Architecture Detection (3 minutes)

**Sub-skill:** `/code-surgeon-architecture-detector`

**Purpose:** Map system architecture, detect architectural style (monolithic/microservices), identify modules, data flow, and boundaries.

**Input Contract:**
```json
{
  "primary_language": "typescript",
  "frameworks": [...],  // from Phase 1
  "files_selected": [...],  // from Phase 2
  "dependency_graph": {...},  // from Phase 2
  "structural_patterns": [...],  // from Phase 2 (preliminary patterns)
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 300
}
```

**Field Context:**
- `primary_language`, `frameworks`: From Phase 1 output
- `files_selected`, `dependency_graph`, `structural_patterns`: From Phase 2 output
- `repo_root`: User-provided (same as Phases 1-2)
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)
- `timeout_seconds`: Global default (3 minutes for Phase 3)

**Output Contract (Success):**
```json
{
  "architecture_type": "Layered MVC",
  "modules": [
    {
      "name": "Authentication",
      "files": ["src/auth/", "src/middleware/auth.ts"],
      "responsibilities": "User login, JWT validation, session management",
      "dependencies": ["src/utils/", "src/db/"],
      "impact": "critical"
    }
  ],
  "data_flow": [
    "Request → API → Auth → Service → Database",
    "Response ← API ← Service ← Database"
  ],
  "boundaries": {
    "client_server": "src/api/ (Express endpoints)",
    "server_database": "src/db/ (ORM queries)"
  },
  "metrics": {
    "module_count": 5,
    "max_dependency_depth": 3,
    "circular_dependencies": [],
    "modularity_score": 78
  }
}
```

**Error Handling:**
- If architecture unclear: Return best guess with low confidence
- If circular dependencies found: Document and continue
- If timeout: Return partial architecture with modules found so far

**Token Cost:** ~3K-6K tokens

---

### Phase 4: Pattern Identification (3 minutes)

**Sub-skill:** `/code-surgeon-pattern-identifier`

**Purpose:** Extract **deep** architectural and design patterns (Singleton, Factory, Observer, MVC, layering), framework-specific patterns (React hooks, Django models), and implementation patterns (error handling, testing, configuration). Receives structural patterns from Phase 2 and builds on them.

**Note:** This phase performs **deep pattern analysis** (design patterns, architectural patterns, framework conventions). It receives `structural_patterns` from Phase 2 and extracts more sophisticated patterns.

**Input Contract:**
```json
{
  "primary_language": "typescript",
  "primary_framework": "React",
  "files_selected": [...],  // from Phase 2
  "dependency_graph": {...},  // from Phase 2
  "structural_patterns": [...],  // from Phase 2 (preliminary patterns)
  "architecture_type": "Layered MVC",  // from Phase 3
  "modules": [...],  // from Phase 3
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 300
}
```

**Field Context:**
- `primary_language`, `primary_framework`: From Phase 1 output
- `files_selected`, `dependency_graph`, `structural_patterns`: From Phase 2 output
- `architecture_type`, `modules`: From Phase 3 output
- `repo_root`: User-provided (same as Phases 1-3)
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)
- `timeout_seconds`: Global default (3 minutes for Phase 4)

**Output Contract (Success):**
```json
{
  "patterns": [
    {
      "type": "Design Pattern",
      "name": "Singleton",
      "location": "src/services/Database.ts",
      "description": "Database connection pool",
      "impact": "Single shared database instance across app",
      "confidence": 0.95
    },
    {
      "type": "Architectural Pattern",
      "name": "Custom Hook Pattern (React)",
      "locations": ["src/hooks/useAuth.ts", "src/hooks/useFetch.ts"],
      "description": "Encapsulates state logic in reusable hooks",
      "impact": "Consistent state management across components",
      "confidence": 0.98
    },
    {
      "type": "Communication Pattern",
      "name": "REST API",
      "location": "src/api/",
      "description": "HTTP-based API with standard CRUD endpoints",
      "impact": "Standard HTTP communication with clients",
      "confidence": 0.99
    }
  ],
  "pattern_count": 12,
  "most_common_pattern": "Custom Hook Pattern"
}
```

**Error Handling:**
- If no patterns found: Continue with empty patterns array
- If confidence low (<0.6): Mark as uncertain
- If timeout: Return patterns found so far

**Token Cost:** ~3K-6K tokens

---

### Phase 5: Tech Stack Analysis (2 minutes)

**Sub-skill:** Built-in analysis from Phase 1 framework output + dependency inspection

**Purpose:** Assess tech stack quality, version freshness, maintenance status, community size, upgrade opportunities, deprecated packages.

**Output Contract (Generated from Phase 1 data):**
```json
{
  "tech_stack": {
    "runtime": "Node.js 18.x",
    "frontend": {
      "framework": "React 18.2.0",
      "status": "modern",
      "notes": "Latest stable, actively maintained"
    },
    "backend": {
      "framework": "Express 4.18.2",
      "orm": "Sequelize 6.35.0",
      "status": "stable",
      "notes": "Production-ready but not latest"
    },
    "database": "PostgreSQL 14",
    "testing": "Jest 29.5.0",
    "build": "Webpack 5.88.0"
  },
  "upgrade_opportunities": [
    {
      "package": "Sequelize",
      "current": "6.35.0",
      "latest": "6.35.1",
      "severity": "patch"
    }
  ],
  "deprecated_packages": []
}
```

**Token Cost:** ~1K tokens

---

### Phase 6: Risk Identification (2 minutes)

**Sub-skill:** `/code-surgeon-risk-analyzer`

**Purpose:** Identify security risks, technical risks, and architectural risks in the codebase.

**Input Contract:**
```json
{
  "files_selected": [...],  // from Phase 2
  "primary_language": "typescript",
  "frameworks": [...],  // from Phase 1
  "architecture_type": "Layered MVC",  // from Phase 3
  "modules": [...],  // from Phase 3
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 300
}
```

**Field Context:**
- `files_selected`, `primary_language`, `frameworks`: From Phase 1-2 outputs
- `architecture_type`, `modules`: From Phase 3 output
- `repo_root`: User-provided (same as Phases 1-5)
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)
- `timeout_seconds`: Global default (2 minutes for Phase 6)

**Output Contract (Success):**
```json
{
  "risks": [
    {
      "severity": "HIGH",
      "type": "Security",
      "title": "Hardcoded API keys in config",
      "location": "src/config/secrets.ts:45",
      "description": "API keys hardcoded in source",
      "impact": "Exposed credentials if repo compromised",
      "remediation": "Move to environment variables (.env)"
    },
    {
      "severity": "MEDIUM",
      "type": "Technical",
      "title": "Outdated dependency",
      "location": "package.json",
      "description": "Express-validator 7.0.0 (current 8.0.1)",
      "impact": "Missing security patches",
      "remediation": "npm update express-validator"
    },
    {
      "severity": "MEDIUM",
      "type": "Architectural",
      "title": "No error handling in middleware",
      "location": "src/middleware/errorHandler.ts",
      "description": "Missing catch-all error handler",
      "impact": "Unhandled errors crash server",
      "remediation": "Add try-catch to all async functions"
    }
  ],
  "risk_summary": {
    "critical_count": 0,
    "high_count": 1,
    "medium_count": 2,
    "low_count": 3
  }
}
```

**Error Handling:**
- If no risks found: Return empty risks array (success)
- If timeout: Return risks found so far
- If PII/secrets detected: Mark as HIGH risk but don't output raw content

**Token Cost:** ~3K-6K tokens

---

### Discovery Mode Sub-Skill Routing Table

| Phase | Sub-Skill | Input Source | Output Used By | Token Budget | Duration |
|-------|-----------|--------------|----------------|--------------|----------|
| 1 | framework-detector | User repo_root | Phases 2-6 | ~1K | 2 min |
| 2 | context-researcher | Phase 1 + user repo | Phases 3-6 | ~30-60K | 5 min |
| 3 | architecture-detector | Phases 1-2 | Phases 4-6, Report | ~3-6K | 3 min |
| 4 | pattern-identifier | Phases 1-3 | Phase 6, Report | ~3-6K | 3 min |
| 5 | (Built-in) | Phase 1 data | Report | ~1K | 2 min |
| 6 | risk-analyzer | Phases 1-5 | Report | ~3-6K | 2 min |

---

### Token Budgeting by Depth Mode

**QUICK Mode (5 min, ~30K tokens, 85% accuracy)**
- Phase 1: 1K (framework detection)
- Phase 2: 14K (Tier 1 + Tier 2, no Tier 3)
- Phase 3: 2K (basic architecture, 2-4 modules)
- Phase 4: 2K (3-5 patterns only)
- Phase 5: 1K (tech stack overview)
- Phase 6: 2K (high-risk items only)
- Output: Markdown (audit report)
- Reserve: 8K for formatting
- **Total: 30K**

**STANDARD Mode (15 min, ~60K tokens, 95% accuracy) ← DEFAULT**
- Phase 1: 1K (full framework detection)
- Phase 2: 35K (Tier 1 + Tier 2 + partial Tier 3)
- Phase 3: 5K (full architecture, 4-6 modules)
- Phase 4: 5K (7-10 patterns with examples)
- Phase 5: 1K (comprehensive tech stack)
- Phase 6: 5K (all risks by severity)
- Output: Markdown + JSON
- Reserve: 8K for formatting
- **Total: 60K**

**DEEP Mode (30 min, ~90K tokens, 99% accuracy)**
- Phase 1: 1K (full framework detection)
- Phase 2: 58K (all Tier 1 + Tier 2 + Tier 3, full patterns)
- Phase 3: 8K (detailed architecture, 6-8 modules)
- Phase 4: 8K (12-15 patterns with 10-15 line examples)
- Phase 5: 2K (comprehensive tech stack with history)
- Phase 6: 8K (all risks + remediation code)
- Output: Markdown + JSON + Interactive
- Reserve: 5K for formatting
- **Total: 90K**

---

### Depth Mode Behavior Details

**QUICK Mode (Discovery)**
- Loads only Tier 1 (direct impact files)
- Pattern analysis minimal (3-5 patterns)
- Architecture overview only (2-4 major modules)
- High-severity risks only (Critical, High)
- No historical analysis
- No alternative architecture suggestions
- Output: Markdown report only

**STANDARD Mode (Discovery) - DEFAULT**
- Loads Tier 1 + Tier 2 + selective Tier 3
- Pattern analysis moderate (7-10 patterns)
- Full architecture with data flow (4-8 modules)
- All risks documented (all severity levels)
- Team conventions identified
- Upgrade recommendations included
- Output: Markdown report + JSON structured data

**DEEP Mode (Discovery)**
- Loads all three tiers (complete context)
- Comprehensive pattern analysis (12-15 patterns)
- Detailed architecture with alternatives (6-10 modules)
- All risks + specific remediation code
- File history and change frequency
- Full dependency graph with versions
- Performance metrics and bottleneck analysis
- Historical trends and technical debt timeline
- Output: Markdown report + JSON data + Interactive navigator

---

### Error Handling for Discovery Mode

**Sub-Skill Invocation Failure**
```
Phase [N] failed to complete.
├─ First attempt: Retry once (wait 5 seconds)
├─ If still fails:
│  ├─ Save state.json immediately
│  ├─ Show: "Phase [N] failed. Session saved."
│  ├─ Show session ID: surgeon-20250213-abc123xyz
│  ├─ Suggest: "/code-surgeon-resume surgeon-20250213-abc123xyz"
│  └─ Stop execution
└─ Rationale: Prevents loss of work, allows resume from checkpoint
```

**Token Budget Exceeded**
```
Approaching 85% of budget (51K/60K):
├─ Log warning
├─ Continue with existing files
└─ Rationale: Safety threshold to prevent mid-phase overrun

Exceed 100% of budget (>60K):
├─ Stop loading new files
├─ Analyze what was loaded
├─ Save state.json
├─ Show: "Exceeded token budget for STANDARD mode"
├─ Offer: (a) Generate report with loaded data, (b) Switch to QUICK mode and retry, (c) Resume from checkpoint
└─ Rationale: Prevents silent token overflow
```

**Repository Issues**
```
Repository not found:
├─ Show error immediately in Phase 1
├─ Stop execution (no state to save)
└─ Suggest: "Check repo path and retry"

Repository unreadable:
├─ Show permission error in Phase 1
├─ Stop execution
└─ Suggest: "Check file permissions"

Analysis timeout in Phase N:
├─ Return partial results from completed phases
├─ Mark incomplete status
├─ Suggest: Switch to QUICK mode for faster analysis
└─ Offer: Save and retry later
```

**PII/Secrets Detection**
```
During analysis, if API keys, passwords, or PII found:
├─ Mark as HIGH security risk (don't output raw values)
├─ Document location and type
├─ Continue analysis (not blocking)
├─ Highlight in Risk section
└─ Note: PII detection is advisory, not enforcement
```

---

### Test Scenarios

These 5 scenarios verify discovery mode works across common use cases:

#### Scenario 1: New Team Onboarding

**User Request:** "I just joined the team. How does our system work?"

**Command:** `/code-surgeon --mode=discovery --depth=STANDARD`

**Expected Flow:**
1. Phase 1: Detect React + Node.js + TypeScript
2. Phase 2: Identify 40-50 files across 3 tiers
3. Phase 3: Map layered MVC architecture with 4-6 modules
4. Phase 4: Find 8-12 patterns (React hooks, Context, middleware chains)
5. Phase 5: Report tech stack (React 18, Express 4, PostgreSQL 14)
6. Phase 6: Identify 3-5 critical risks (outdated deps, missing error handling)

**Output:**
- Architecture overview showing main modules
- Tech stack explanation
- 8-12 key patterns they'll see
- Top 3-5 risks and remediation

**Success Criteria:**
- New engineer can understand system without prior context
- Clear learning path implied
- Patterns guide code reading strategy

---

#### Scenario 2: Legacy Codebase Audit

**User Request:** "Understand 15-year-old monolithic Rails app"

**Command:** `/code-surgeon --mode=discovery --depth=DEEP`

**Expected Flow:**
1. Phase 1: Detect Rails + legacy Ruby version
2. Phase 2: Load all tiers (may hit token budget)
3. Phase 3: Map monolithic architecture with tight coupling
4. Phase 4: Find legacy patterns (callbacks, before_filters, string-based deps)
5. Phase 5: Assess tech stack (Rails 5, Ruby 2.6 - outdated)
6. Phase 6: High risk count (security, deprecation, scalability)

**Output:**
- Monolithic architecture diagram
- Legacy patterns documented
- Upgrade roadmap (Rails 7, Ruby 3.x)
- Security vulnerabilities (10-15 found)
- Refactoring priorities

**Success Criteria:**
- Legacy patterns clearly identified
- Modernization path clear
- Security risks documented
- Effort estimates provided

---

#### Scenario 3: Security Risk Assessment

**User Request:** "Audit our codebase for security issues"

**Command:** `/code-surgeon --mode=discovery --depth=DEEP`

**Expected Flow:**
1-4. Standard discovery phases
5. Phase 5: List all dependencies with version status
6. Phase 6: Deep security scan (hardcoded secrets, SQL injection, XSS, auth gaps)

**Output:**
- Dependency vulnerability report
- Code-level security issues with locations
- Authentication/authorization assessment
- Input validation review
- Remediation effort per issue

**Success Criteria:**
- All HIGH/CRITICAL risks identified
- Remediation code provided (DEEP mode)
- Impact severity clear
- Implementation effort estimated

---

#### Scenario 4: Pattern Analysis & Discovery

**User Request:** "What patterns does this project use?"

**Command:** `/code-surgeon --mode=discovery --depth=STANDARD`

**Expected Flow:**
1-2. Framework + context detection
3-4. Identify architectural and framework-specific patterns
5. Tech stack assessment (what's current, what's stale)
6. Risk analysis (compatibility, deprecation)

**Output:**
- Pattern inventory (8-12 patterns)
- Pattern usage locations
- Framework conventions identified
- Tech stack assessment
- Upgrade recommendations

**Success Criteria:**
- Patterns documented with examples
- Framework specifics highlighted
- Upgrade path clear
- Breaking change implications understood

---

#### Scenario 5: Microservices Architecture Mapping

**User Request:** "Map system architecture of microservice platform"

**Command:** `/code-surgeon --mode=discovery --depth=DEEP`

**Expected Flow:**
1. Phase 1: Detect multiple frameworks (API Gateway, 3-4 services)
2. Phase 2: Load files from each service + gateway
3. Phase 3: Map microservice architecture with service boundaries
4. Phase 4: Find inter-service communication patterns
5. Phase 5: Tech stack per service (consistent or fragmented?)
6. Phase 6: Architecture risks (auth, transactions, monitoring)

**Output:**
- System architecture diagram with services
- Service dependencies
- Communication patterns (REST, gRPC, message queue)
- Data consistency challenges
- Observability gaps

**Success Criteria:**
- Service boundaries clear
- Inter-service dependencies mapped
- Communication patterns identified
- Scaling challenges documented

---

### Integration with Hub-and-Spoke Model

**Discovery Mode in Hub-and-Spoke:**

Discovery mode follows a **strictly sequential pipeline** (not parallel) with each phase depending on prior phase outputs.

```
          User Input
          "Analyze this codebase"
               ↓
    ┌─────────────────────┐
    │  Task Classifier    │
    │  (→ Discovery mode) │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 1: Framework Detection       │
    │  (Detects tech stack, languages)    │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 2: Context Research          │
    │  (Analyzes files, dependencies)     │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 3: Architecture Detection    │
    │  (Maps system architecture)         │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 4: Pattern Identification    │
    │  (Extracts design + framework patterns) │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 5: Tech Stack Analysis       │
    │  (Assesses versions, updates)       │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 6: Risk Identification       │
    │  (Identifies security, tech risks)  │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │    Audit Report Generator           │
    │  (Markdown + JSON + Interactive)    │
    └──────────┬──────────────────────────┘
               ↓
    Audit Report Output (varies by depth)
```

**Sequential Guarantee:** Each phase completes before the next begins. Enables resumption from any checkpoint and predictable token budgeting.

---

### Continuity: Phase Entry/Exit Contracts

Each phase passes data to the next in strict format. **Two-tier pattern analysis:** Phase 2 identifies structural patterns; Phase 4 identifies deep patterns.

| From Phase | Data Passed | To Phase | Used For |
|------------|------------|----------|----------|
| 1 (Framework) | `primary_language`, `frameworks[]`, `is_monorepo` | 2-6 | Context, pattern matching, risk assessment |
| 2 (Context) | `files_selected[]`, `dependency_graph`, `structural_patterns[]`, `team_conventions` | 3-6 | Architecture analysis, baseline for Phase 4 deep patterns, risk assessment |
| 3 (Architecture) | `architecture_type`, `modules[]`, `data_flow`, `boundaries`, `metrics` | 4-6, Report | Module understanding, context for Phase 4 deep patterns, risk prioritization |
| 4 (Patterns - Deep) | `patterns[]` (design + architectural + framework), `pattern_count`, `confidence` | Report | Deep pattern documentation, learning path |
| 5 (Tech Stack) | `tech_stack`, `upgrade_opportunities`, `deprecated_packages` | Report | Modernization roadmap |
| 6 (Risks) | `risks[]`, `risk_summary`, `remediation_steps` | Report | Risk prioritization, remediation planning |

**Pattern Analysis Clarification:**
- **Phase 2 (Structural Patterns):** Identifies code organization patterns, naming conventions, folder structure patterns from file analysis
- **Phase 4 (Deep Patterns):** Builds on Phase 2 structural patterns to extract design patterns (Singleton, Factory, Observer), architectural patterns (MVC, layering), framework-specific patterns (React hooks, Django models), implementation patterns (error handling, testing)

---

### Session Management for Discovery

**State File Location:**
```
.claude/planning/sessions/<session-id>/
├─ state.json              ← Complete session state (all phases)
├─ AUDIT.md                ← Human-readable audit report
├─ audit.json              ← Machine-readable data
├─ interactive.json        ← CLI navigation data
└─ logs/
   └─ discovery.log        ← Phase-by-phase execution log
```

**Resumption Logic:**
- If Phase 2 times out: Save state after Phase 1, resume at Phase 2
- If Phase 5 fails: Retry once, then save and resume
- If token budget exceeded: Mark where overflow occurred, resume with QUICK mode

---

### Discovery Mode Success Criteria

A successful discovery analysis meets ALL of these:

- [ ] 6-phase workflow completed without critical errors
- [ ] All 5 sub-skills invoked with valid input/output contracts
- [ ] Token budget not exceeded (with <10% safety margin)
- [ ] Audit report generated in required format(s)
- [ ] Architecture clearly documented with module responsibilities
- [ ] 5-10 patterns identified with locations and descriptions
- [ ] All HIGH/CRITICAL risks documented with remediation
- [ ] No hallucinated files or patterns
- [ ] Session resumable if interrupted
- [ ] Depth mode behavior correct (QUICK < STANDARD < DEEP)

---

## Review Mode Orchestration

Review mode performs deep safety assessment of proposed changes to generate a **Risk Report** with breaking change detection and pre-flight validation. This section details the exact 6-phase orchestration workflow.

### Executive Summary

**Duration:** 16 minutes (STANDARD) | **Token Budget:** 60K | **Accuracy:** 95%

Review mode routes through 6 core sub-skills in strict sequence to assess change impact, detect breaking changes, and validate safety:

```
Framework Detection (2 min)
    ↓
Context Research (5 min)
    ↓
Impact Analysis (3 min)
    ↓
Breaking Change Detection (3 min)
    ↓
Pre-Flight Validation (2 min)
    ↓
Safety Verification (1 min)
    ↓
Risk Report (Generated Markdown)
```

### Phase 1: Framework Detection (2 minutes)

**Sub-skill:** `/code-surgeon-framework-detector`

**Purpose:** Detect tech stack, programming languages, frameworks, versions, and monorepo structure (identical to Discovery Phase 1).

**Input Contract:**
```json
{
  "repo_root": "/absolute/path/to/repo",
  "timeout_ms": 120000
}
```

**Field Context:**
- `repo_root`: User-provided (absolute path to repository)
- `timeout_ms`: Global default (2 minutes for Phase 1)

**Output Contract (Success):**
```json
{
  "primary_language": "typescript",
  "primary_framework": "React",
  "frameworks": [
    {
      "name": "React",
      "version": "18.2.0",
      "language": "typescript",
      "category": "frontend"
    }
  ],
  "languages": [
    {"language": "typescript", "file_count": 145, "percentage": 85}
  ],
  "is_monorepo": false,
  "has_typescript": true,
  "has_testing": true,
  "has_documentation": true,
  "confidence": 0.96
}
```

**Error Handling:**
- If repo not found: Stop immediately, return "Repository not found"
- If unreadable: Stop immediately, return "Repository access denied"
- If timeout: Return partial results with low confidence flag

**Token Cost:** ~1K tokens

---

### Phase 2: Context Research (5 minutes)

**Sub-skill:** `/code-surgeon-context-researcher`

**Purpose:** Analyze codebase structure, build dependency graph, identify structural patterns, and find team conventions.

**Input Contract:**
```json
{
  "issue_type": "refactor",
  "requirements": ["Assess impact of proposed change"],
  "primary_language": "typescript",
  "frameworks": [...],  // from Phase 1
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 300
}
```

**Field Context:**
- `primary_language`, `frameworks`: From Phase 1 output (previous phase)
- `repo_root`: User-provided (same as Phase 1)
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)
- `timeout_seconds`: Global default (5 minutes for Phase 2)

**Output Contract (Success):**
```json
{
  "files_selected": [
    {
      "path": "src/auth.ts",
      "tier": 1,
      "size_bytes": 2400,
      "relevance": "critical",
      "reason": "Core authentication module, likely affected by change"
    }
  ],
  "file_count": {
    "tier_1": 8,
    "tier_2": 25,
    "tier_3": 12,
    "total": 45
  },
  "dependency_graph": {
    "src/auth.ts": {
      "imports": ["src/utils.ts"],
      "imported_by": ["src/api.ts", "src/middleware/auth.ts"],
      "impact": "critical"
    }
  },
  "structural_patterns": [
    {
      "name": "Custom Hook Pattern",
      "example_file": "src/hooks/useAuth.ts",
      "description": "Custom hooks for state management",
      "location": "src/hooks/**/*.ts"
    }
  ],
  "team_conventions": [
    "camelCase for functions, PascalCase for components",
    "Always use try-catch in async functions"
  ]
}
```

**Error Handling:**
- If timeout: Return partial files found (Tier 1 only), mark as incomplete
- If token budget exceeded: Remove Tier 3 patterns, retry with Tier 1-2 only
- If no files found: Stop, return "Repository structure unreadable"

**Token Cost:** 30K-90K tokens (varies by depth mode)

---

### Phase 3: Impact Analysis (3 minutes)

**Sub-skill:** `/code-surgeon-impact-analyzer` (new in Review mode)

**Purpose:** Analyze proposed change, map affected files, assess scope of impact (direct and transitive dependents), identify affected tests, and build impact summary.

**Input Contract:**
```json
{
  "change_description": "Rename authenticate() function to validateCredentials()",
  "change_type": "api",
  "primary_language": "typescript",
  "frameworks": [...],  // from Phase 1
  "files_selected": [...],  // from Phase 2
  "dependency_graph": {...},  // from Phase 2
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 180
}
```

**Field Context:**
- `change_description`: User-provided requirement or change description
- `change_type`: Categorized as "api", "data", "behavior", "dependency", or "other"
- `files_selected`, `dependency_graph`: From Phase 2 output
- `repo_root`: User-provided (same as Phases 1-2)
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)

**Output Contract (Success):**
```json
{
  "scope": {
    "files_affected": 15,
    "modules_affected": 3,
    "layers_affected": ["api", "service", "middleware"]
  },
  "impact_analysis": {
    "direct_dependents": [
      {
        "file": "src/api/auth.ts",
        "type": "import",
        "impact": "Uses renamed function - needs update"
      }
    ],
    "transitive_dependents": [
      {
        "file": "src/components/Login.tsx",
        "depth": 2,
        "impact": "Indirect: uses module that imports change"
      }
    ],
    "external_users": [
      {
        "type": "npm_package",
        "name": "@myorg/auth-utils",
        "version": "2.1.0",
        "impact": "Breaking for v2.1.0"
      }
    ]
  },
  "affected_tests": {
    "unit_tests": 12,
    "integration_tests": 3,
    "e2e_tests": 1
  }
}
```

**Error Handling:**
- If change description unclear: Request clarification
- If no affected files found: Return low-impact assessment
- If timeout: Return partial impact analysis with files found

**Token Cost:** ~3K-6K tokens

---

### Phase 4: Breaking Change Detection (3 minutes)

**Sub-skill:** `/code-surgeon-breaking-change-detector` (new in Review mode)

**Purpose:** Detect and categorize breaking changes (API changes, data changes, behavior changes, dependency changes), assess severity, identify migration paths, and provide effort estimates.

**Input Contract:**
```json
{
  "change_description": "Rename authenticate() function to validateCredentials()",
  "change_type": "api",
  "files_affected": ["src/auth.ts"],
  "impact_analysis": {...},  // from Phase 3
  "dependency_graph": {...},  // from Phase 2
  "primary_language": "typescript",
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 180
}
```

**Field Context:**
- `change_description`: From user input
- `files_affected`: From Phase 3 impact analysis
- `dependency_graph`: From Phase 2 output
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)

**Output Contract (Success):**
```json
{
  "breaking_changes": [
    {
      "type": "api",
      "severity": "critical",
      "title": "Function signature changed",
      "location": "src/auth.ts:45",
      "old_signature": "authenticate(username: string, password: string) -> Promise<User>",
      "new_signature": "validateCredentials(credentials: LoginCredentials) -> Promise<AuthToken>",
      "impact": "All callers must update to new signature",
      "affected_locations": [
        "src/api/auth.ts:20",
        "src/hooks/useAuth.ts:15",
        "tests/auth.test.ts:30"
      ],
      "migration_path": "Update all calls from authenticate(username, password) to validateCredentials({username, password})",
      "effort_estimate": "30 minutes"
    },
    {
      "type": "data",
      "severity": "high",
      "title": "Database schema migration required",
      "location": "src/db/schema.sql",
      "change": "Added required column 'mfa_enabled' to users table",
      "impact": "Existing users must have default value or explicit update",
      "affected_locations": ["src/db/migrations/", "src/models/User.ts"],
      "migration_path": "Run migration: ALTER TABLE users ADD COLUMN mfa_enabled BOOLEAN DEFAULT false;",
      "effort_estimate": "5 minutes"
    }
  ],
  "breaking_change_count": 2,
  "critical_count": 1,
  "high_count": 1,
  "medium_count": 0
}
```

**Error Handling:**
- If no breaking changes detected: Return empty array (success)
- If timeout: Return breaking changes found so far
- If uncertain: Mark with low confidence

**Token Cost:** ~3K-6K tokens

---

### Phase 5: Pre-Flight Validation (2 minutes)

**Sub-skill:** `/code-surgeon-preflight-validator` (new in Review mode)

**Purpose:** Validate change completeness, assess migration path, create pre-flight checklist with action items and validation steps.

**Input Contract:**
```json
{
  "change_description": "Rename authenticate() function to validateCredentials()",
  "breaking_changes": [...],  // from Phase 4
  "impact_analysis": {...},  // from Phase 3
  "files_affected": 15,
  "affected_tests": {
    "unit_tests": 12,
    "integration_tests": 3,
    "e2e_tests": 1
  },
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 120
}
```

**Field Context:**
- `breaking_changes`: From Phase 4 output
- `impact_analysis`: From Phase 3 output
- `files_affected`: Count from Phase 3
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)

**Output Contract (Success):**
```json
{
  "validation": {
    "completeness": "high",
    "risks_identified": true,
    "migration_path_clear": true
  },
  "preflight_checklist": {
    "code_review": {
      "item": "Code reviewed by [name]",
      "status": "pending",
      "why": "Large API change affecting 15 files"
    },
    "test_coverage": {
      "item": "Run full test suite",
      "status": "pending",
      "why": "16 tests affected by this change"
    },
    "backward_compatibility": {
      "item": "Check backward compatibility",
      "status": "pending",
      "why": "1 critical breaking change detected"
    },
    "documentation": {
      "item": "Update API documentation",
      "status": "pending",
      "why": "Function signature changed"
    },
    "downstream": {
      "item": "Notify npm package users",
      "status": "pending",
      "why": "External package depends on this API"
    },
    "staging": {
      "item": "Test in staging environment",
      "status": "pending",
      "why": "Large change requires validation in realistic environment"
    }
  }
}
```

**Error Handling:**
- If no validation needed: Return minimal checklist
- If timeout: Return checklist items found so far
- If uncertain: Mark items as "recommended"

**Token Cost:** ~2K-4K tokens

---

### Phase 6: Safety Verification (1 minute)

**Sub-skill:** `/code-surgeon-safety-verifier` (new in Review mode)

**Purpose:** Final safety verification - assess data loss risk, verify reversibility, check error handling coverage, validate test coverage, and provide final recommendation.

**Input Contract:**
```json
{
  "change_description": "Rename authenticate() function to validateCredentials()",
  "breaking_changes": [...],  // from Phase 4
  "affected_tests": {
    "unit_tests": 12,
    "integration_tests": 3,
    "e2e_tests": 1
  },
  "files_affected": 15,
  "preflight_checklist": {...},  // from Phase 5
  "repo_root": "/absolute/path/to/repo",
  "depth_mode": "standard",
  "timeout_seconds": 60
}
```

**Field Context:**
- `breaking_changes`: From Phase 4 output
- `affected_tests`: From Phase 3 output
- `preflight_checklist`: From Phase 5 output
- `depth_mode`: Global configuration (QUICK/STANDARD/DEEP)

**Output Contract (Success):**
```json
{
  "safety_verification": {
    "data_loss_risk": "low",
    "reversibility": "yes - can revert to old signature with deprecation",
    "error_handling": "complete",
    "test_coverage": {
      "changed_lines": 45,
      "covered_lines": 43,
      "coverage_percentage": 95.6
    },
    "recommendation": "Safe to merge with pre-flight checklist completed"
  },
  "risk_level": "high",
  "final_verdict": "PROCEED WITH CAUTION - 1 critical breaking change, requires coordinated migration"
}
```

**Error Handling:**
- If blocking risk found: Mark as BLOCKED
- If incomplete data: Return partial verification
- If timeout: Return verification completed so far

**Token Cost:** ~1K-2K tokens

---

### Review Mode Sub-Skill Routing Table

| Phase | Sub-Skill | Input Source | Output Used By | Token Budget | Duration |
|-------|-----------|--------------|----------------|--------------|----------|
| 1 | framework-detector | User repo_root | Phases 2-6 | ~1K | 2 min |
| 2 | context-researcher | Phase 1 + user repo | Phases 3-6 | ~30-60K | 5 min |
| 3 | impact-analyzer | Phases 1-2 + change description | Phases 4-6, Report | ~3-6K | 3 min |
| 4 | breaking-change-detector | Phases 1-3 + change | Phases 5-6, Report | ~3-6K | 3 min |
| 5 | preflight-validator | Phases 3-4 | Phase 6, Report | ~2-4K | 2 min |
| 6 | safety-verifier | Phases 3-5 | Report | ~1-2K | 1 min |

---

### Token Budgeting by Depth Mode

**QUICK Mode (5 min, ~30K tokens, 85% accuracy)**
- Phase 1: 1K (framework detection)
- Phase 2: 14K (Tier 1 + Tier 2, no Tier 3)
- Phase 3: 2K (direct impact only, no transitive)
- Phase 4: 2K (major breaking changes only)
- Phase 5: 2K (basic checklist)
- Phase 6: 1K (final verdict only)
- Output: Markdown (risk report)
- Reserve: 6K for formatting
- **Total: 30K**

**STANDARD Mode (15 min, ~60K tokens, 95% accuracy) ← DEFAULT**
- Phase 1: 1K (full framework detection)
- Phase 2: 35K (Tier 1 + Tier 2 + partial Tier 3)
- Phase 3: 5K (direct + transitive impact analysis)
- Phase 4: 5K (all breaking changes with migration paths)
- Phase 5: 4K (comprehensive pre-flight checklist)
- Phase 6: 2K (complete safety verification)
- Output: Markdown + JSON
- Reserve: 8K for formatting
- **Total: 60K**

**DEEP Mode (30 min, ~90K tokens, 99% accuracy)**
- Phase 1: 1K (full framework detection)
- Phase 2: 58K (all Tier 1 + Tier 2 + Tier 3)
- Phase 3: 8K (detailed impact analysis with visualizations)
- Phase 4: 8K (all breaking changes + alternatives + rollback procedures)
- Phase 5: 6K (detailed pre-flight with step-by-step validation)
- Phase 6: 3K (complete safety verification + security impact)
- Output: Markdown + JSON + Interactive
- Reserve: 6K for formatting
- **Total: 90K**

---

### Depth Mode Behavior Details

**QUICK Mode (Review)**
- Analyzes only direct dependents (no transitive)
- Only critical and high severity breaking changes
- Basic pre-flight checklist (4-6 items)
- Final verdict only (no detailed verification)
- Output: Markdown risk report summary

**STANDARD Mode (Review) - DEFAULT**
- Complete impact analysis (direct + transitive)
- All breaking changes by severity (critical, high, medium)
- Full pre-flight checklist (8-12 items)
- Complete safety verification with recommendations
- Output: Markdown + JSON structured data

**DEEP Mode (Review)**
- Exhaustive impact analysis with visualizations
- All breaking changes + alternative approaches + rollback procedures
- Detailed pre-flight with step-by-step validation and effort estimates
- Safety verification + security impact + cost estimation
- Performance impact analysis
- Output: Markdown report + JSON data + Interactive navigator

---

### Error Handling for Review Mode

**Sub-Skill Invocation Failure**
```
Phase [N] failed to complete.
├─ First attempt: Retry once (wait 5 seconds)
├─ If still fails:
│  ├─ Save state.json immediately
│  ├─ Show: "Phase [N] failed. Session saved."
│  ├─ Show session ID: surgeon-20250213-abc123xyz
│  ├─ Suggest: "/code-surgeon-resume surgeon-20250213-abc123xyz"
│  └─ Stop execution
└─ Rationale: Prevents loss of work, allows resume from checkpoint
```

**Token Budget Exceeded**
```
Approaching 85% of budget (51K/60K):
├─ Log warning
├─ Continue with existing impact analysis
└─ Rationale: Safety threshold to prevent mid-phase overrun

Exceed 100% of budget (>60K):
├─ Stop loading new files
├─ Analyze what was loaded
├─ Save state.json
├─ Show: "Exceeded token budget for STANDARD mode"
├─ Offer: (a) Generate report with loaded data, (b) Switch to QUICK mode and retry
└─ Rationale: Prevents silent token overflow
```

**Repository Issues**
```
Repository not found:
├─ Show error immediately in Phase 1
├─ Stop execution
└─ Suggest: "Check repo path and retry"

Analysis timeout in Phase N:
├─ Return partial results from completed phases
├─ Mark incomplete status
├─ Suggest: Switch to QUICK mode for faster analysis
└─ Offer: Save and retry later
```

---

### Risk Assessment Categories

Review mode categorizes risks into severity levels:

#### Critical Risks (STOP AND REVIEW)

**Characteristics:** Data loss, security exposure, system outage, breaking changes with no clear migration path

**Concrete Examples:**

1. **Data Loss Risk**
   ```typescript
   // CRITICAL: ALTER TABLE removes column permanently
   ALTER TABLE users DROP COLUMN email_verified;
   // Migration path: None - data permanently lost
   ```

2. **Security Exposure**
   ```typescript
   // CRITICAL: Removing authentication check
   - app.get('/api/admin/users', authenticateUser, listAllUsers);
   + app.get('/api/admin/users', listAllUsers);  // Missing auth!
   // Migration path: BLOCKED - security vulnerability
   ```

3. **API Breaking Change (No Deprecation)**
   ```typescript
   // CRITICAL: Renamed export without backward compatibility
   - export function validatePassword(pwd: string): boolean
   + export function checkPassword(pwd: string): boolean
   // Migration path: All 12 callers must update simultaneously
   ```

**Action:** Do not proceed without explicit risk mitigation and stakeholder review.

---

#### High Risks (CAREFUL REVIEW)

**Characteristics:** Breaking changes with clear migration path, third-party impacts, non-trivial refactoring

**Concrete Examples:**

1. **Breaking API Change**
   ```typescript
   // HIGH: Function signature changed, but migration is straightforward
   - export function authenticate(username: string, password: string)
   + export function authenticate(credentials: {username: string; password: string})
   // Migration path: Update all 8 callers from authenticate(u, p) to authenticate({username: u, password: p})
   // Effort: 20 minutes
   ```

2. **Type Change in Return Value**
   ```typescript
   // HIGH: Return type changed from object to array
   - function getUsers(): User { ... }
   + function getUsers(): User[] { ... }
   // Migration path: Update callers expecting single User object
   // External dependency: @myorg/api-sdk depends on this
   ```

3. **Major Version Bump**
   ```json
   // HIGH: Upgrading dependency with major breaking changes
   {
     "react": "17.0.0" → "18.0.0"
   }
   // Migration path: Update Hook usage, Concurrent features
   // Effort: 3-5 hours for large codebase
   ```

**Action:** Document breaking changes and provide migration guide before merging.

---

#### Medium Risks (NOTED)

**Characteristics:** Moderate impact changes, internal refactoring with external touch points

**Concrete Examples:**

1. **Module Move with Import Path Changes**
   ```typescript
   // MEDIUM: Moving utility affects import paths
   - import { formatDate } from './utils/formatDate';
   + import { formatDate } from './shared/date/formatDate';
   // Migration path: Update 5 import statements
   // Impact: Internal, but test files need updates
   ```

2. **State Structure Refactoring**
   ```typescript
   // MEDIUM: Internal state restructured
   - return {user: {id, name, email}}
   + return {userId: id, userName: name, userEmail: email}
   // Migration path: Update 3 components consuming this state
   // Impact: Internal but breaks consumer code
   ```

3. **Error Message Changes**
   ```typescript
   // MEDIUM: Error handling modified
   - throw new Error('Invalid user');
   + throw new ValidationError('User data invalid', {field: 'user'});
   // Migration path: Update error handlers
   // Impact: Logging systems depend on error messages
   ```

**Action:** Document changes and notify affected teams.

---

#### Low Risks (PROCEED)

**Characteristics:** Internal changes, optimizations, documentation updates, no breaking changes

**Concrete Examples:**

1. **Internal Variable Rename**
   ```typescript
   // LOW: Only internal, no public API change
   - let userData = fetchUser();
   + let userInfo = fetchUser();
   // No breaking changes, internal only
   ```

2. **Performance Optimization**
   ```typescript
   // LOW: Improves performance without interface change
   - function processItems(items) { return items.map(...); }
   + function processItems(items) { return items.reduce(...); }  // Faster
   // No breaking changes, same interface
   ```

3. **Documentation Update**
   ```typescript
   // LOW: Only updates comments/docs
   - // Get user by id
   + // Fetch user profile by ID, returns cached result if available
   // No code changes, no breaking changes
   ```

**Action:** Proceed normally, document in commit message.

---

### Test Scenarios

These 4 scenarios verify review mode works across common use cases:

#### Scenario 1: Dependency Upgrade

**User Request:** "Is it safe to upgrade React from 17 to 18?"

**Command:** `/code-surgeon "Upgrade React 17 to 18" --mode=review --depth=STANDARD`

**Expected Flow:**
1. Phase 1: Detect React 17.x + Node.js
2. Phase 2: Load relevant files (src/components/, src/hooks/, src/App.tsx)
3. Phase 3: Identify affected files using React (hooks, components, Root API)
4. Phase 4: Detect breaking changes (legacy Root API removed, Hook rules)
5. Phase 5: Create migration checklist (update Hook deps, test concurrent features)
6. Phase 6: Verify safety (high risk but manageable with clear migration path)

**Output:**
- Breaking changes identified (legacy Root API, Hook usage patterns)
- Migration guide per file
- Pre-flight checklist with validation steps
- Risk assessment: HIGH but manageable

**Success Criteria:**
- All breaking changes identified
- Migration path clear for each file type
- Effort estimate provided
- Go/no-go recommendation explicit

---

#### Scenario 2: API Refactoring

**User Request:** "Rename /auth endpoint to /authentication. Will this break anything?"

**Command:** `/code-surgeon "/auth → /authentication refactor" --mode=review --depth=STANDARD`

**Expected Flow:**
1. Phase 1: Detect Node.js + Express
2. Phase 2: Find all references to /auth endpoint
3. Phase 3: Identify 3 internal API callers + 1 external SDK dependency
4. Phase 4: Detect breaking change (endpoint path change, 4 callers affected)
5. Phase 5: Create migration checklist (update clients, notify SDK users)
6. Phase 6: Verify safety (critical: external dependency affected)

**Output:**
- All 4 callers identified with exact locations
- External package impact flagged (users need notice)
- Migration plan (update routes + notify SDK users)
- Effort: 2 hours (refactoring + testing + notifications)

**Success Criteria:**
- External users identified and flagged
- Clear blocking point: external SDK notification needed
- Migration effort estimated
- Recommendation: Safe IF external users notified

---

#### Scenario 3: Pre-Merge Review

**User Request:** "Is this refactoring safe to merge?" (large PR)

**Command:** `/code-surgeon "Large refactoring PR" --mode=review --depth=DEEP`

**Expected Flow:**
1. Phase 1: Detect framework + languages
2. Phase 2: Load all affected files from PR
3. Phase 3: Map complete change impact (15 files, 3 modules)
4. Phase 4: Detect all breaking changes with migration paths
5. Phase 5: Generate detailed pre-flight checklist (tests, reviews, staging)
6. Phase 6: Final safety verdict with conditions

**Output:**
- Detailed impact map showing file-by-file changes
- All breaking changes documented with alternatives
- Step-by-step pre-flight checklist
- Detailed safety verification report
- Go/no-go with specific conditions

**Success Criteria:**
- Every changed file assessed for impact
- All breaking changes have migration paths
- Pre-flight checklist complete and actionable
- Clear go/no-go verdict with conditions

---

#### Scenario 4: Schema Migration

**User Request:** "I'm adding a NOT NULL column to users table. What needs to change?"

**Command:** `/code-surgeon "Add NOT NULL column mfa_enabled to users table" --mode=review --depth=STANDARD`

**Expected Flow:**
1. Phase 1: Detect backend framework + database type
2. Phase 2: Find all code writing to users table (models, migrations, API)
3. Phase 3: Identify scope (3 modules affected, 12 files touched)
4. Phase 4: Detect breaking changes (NOT NULL without default, migration needed)
5. Phase 5: Create migration checklist (add default, handle existing rows, test)
6. Phase 6: Verify safety (high risk without proper migration steps)

**Output:**
- All code paths writing to users table identified
- Breaking change: NOT NULL constraint without default
- Migration strategy: Add default value first, then constraint
- Pre-flight: Coordinate with deployment, test rollback
- Reversibility: Yes, can roll back with procedure

**Success Criteria:**
- All writers to table identified
- Migration strategy clear and reversible
- Rollback procedure documented
- Deployment coordination requirements explicit

---

### Integration with Hub-and-Spoke Model

**Review Mode in Hub-and-Spoke:**

Review mode follows a **strictly sequential pipeline** with each phase depending on prior phase outputs.

```
          User Input
     "requirement"
      --mode=review
            ↓
    ┌─────────────────────┐
    │  Task Classifier    │
    │  (→ Review mode)    │
    └──────────┬──────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 1: Framework Detection       │
    │  (Detects tech stack, languages)    │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 2: Context Research          │
    │  (Analyzes files, dependencies)     │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 3: Impact Analysis           │
    │  (Maps change impact)               │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 4: Breaking Change Detection │
    │  (Identifies breaking changes)      │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 5: Pre-Flight Validation     │
    │  (Creates validation checklist)     │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │  Phase 6: Safety Verification       │
    │  (Final safety assessment)          │
    └──────────┬──────────────────────────┘
               ↓
    ┌─────────────────────────────────────┐
    │    Risk Report Generator            │
    │  (Markdown + JSON + Interactive)    │
    └──────────┬──────────────────────────┘
               ↓
    Risk Report Output (varies by depth)
```

**Sequential Guarantee:** Each phase completes before the next begins. Enables resumption from any checkpoint and predictable token budgeting.

---

### Continuity: Phase Entry/Exit Contracts

Each phase passes data to the next in strict format.

| From Phase | Data Passed | To Phase | Used For |
|------------|------------|----------|----------|
| 1 (Framework) | `primary_language`, `frameworks[]`, `is_monorepo` | 2-6 | Context, impact matching, safety assessment |
| 2 (Context) | `files_selected[]`, `dependency_graph`, `structural_patterns[]` | 3-6 | Impact analysis, change scope, breaking change detection |
| 3 (Impact) | `scope`, `impact_analysis[]`, `affected_tests` | 4-6, Report | Breaking change context, checklist generation |
| 4 (Breaking Changes) | `breaking_changes[]`, `severity_counts` | 5-6, Report | Checklist items, safety verification |
| 5 (Pre-Flight) | `preflight_checklist[]`, `validation_status` | 6, Report | Final verification, recommendation basis |
| 6 (Safety) | `safety_verification`, `final_verdict`, `risk_level` | Report | Risk report summary, go/no-go decision |

---

### Session Management for Review

**State File Location:**
```
.claude/planning/sessions/<session-id>/
├─ state.json              ← Complete session state (all phases)
├─ RISK_REPORT.md          ← Human-readable risk report
├─ risk_report.json        ← Machine-readable data
├─ interactive.json        ← CLI navigation data
└─ logs/
   └─ review.log           ← Phase-by-phase execution log
```

**Resumption Logic:**
- If Phase 2 times out: Save state after Phase 1, resume at Phase 2
- If Phase 4 fails: Retry once, then save and resume
- If token budget exceeded: Mark where overflow occurred, resume with QUICK mode

---

### Review Mode Success Criteria

A successful review analysis meets ALL of these:

- [ ] 6-phase workflow completed without critical errors
- [ ] All 6 sub-skills invoked with valid input/output contracts
- [ ] Token budget not exceeded (with <10% safety margin)
- [ ] Risk report generated in required format(s)
- [ ] All breaking changes identified with severity levels
- [ ] Impact scope clearly documented (files, modules, layers)
- [ ] Pre-flight checklist created with 6-12 actionable items
- [ ] Safety verification complete with final verdict
- [ ] No hallucinated files or changes
- [ ] Session resumable if interrupted
- [ ] Depth mode behavior correct (QUICK < STANDARD < DEEP)
- [ ] Go/no-go decision explicit and justified

---

## Hub-and-Spoke Architecture with Mode-Specific Routing

```
                    User Input
                         ↓
              ┌───────────────────────┐
              │  Task Classifier      │
              │  (Q1-Q4 decision tree)│
              └───────────┬───────────┘
                          ↓
            ┌─────────────────────────────────────────┐
            │         Mode Router (select one)         │
            └──┬────────┬───────────┬──────────┬──────┘
               ↓        ↓           ↓          ↓
          DISCOVERY   REVIEW    OPTIMIZATION  IMPLEMENTATION
            MODE      MODE        MODE        PLANNING MODE
               ↓        ↓           ↓          ↓
         ┌─────────────────────────────────────────────┐
         │  Shared Analysis Pipeline (All Modes)       │
         │  ├─ Phase 1: Framework Detection            │
         │  ├─ Phase 2: Context Research               │
         │  ├─ Phase 3: Mode-Specific Analysis         │
         │  ├─ Phase 4: Pattern/Risk/Impact Analysis   │
         │  ├─ Phase 5: Tech Stack / Planning          │
         │  └─ Phase 6: Risks / Prompts / Validation   │
         └──────────────┬────────────────────────────┘
                        ↓
              ┌──────────────────────┐
              │  Discovery Mode Only │
              ├──────────────────────┤
              │ Specialist Sub-Skills│
              │ ├─ Architecture      │
              │ │   Analyzer (Ph 3)  │
              │ ├─ Pattern           │
              │ │   Identifier (Ph4) │
              │ ├─ Risk Analyzer     │
              │ │   (Ph 6)           │
              │ └─ Output: Audit     │
              │    Report            │
              └──────────┬───────────┘
                         ↓
              ┌──────────────────────┐
              │    Output Generator  │
              │ (Format by depth     │
              │  and mode)           │
              └──────────┬───────────┘
                         ↓
                    Output
              (Audit Report, Plan,
               Recommendations, etc.)
```

**Discovery Mode Sub-Skill Flow:**

```
Phase 1: Framework Detection
         ↓
Phase 2: Context Research
         ↓
Phase 3: Architecture Analyzer (Discovery-Specific)
         ↓
Phase 4: Pattern Identifier (Discovery-Specific)
         ↓
Phase 5: Tech Stack Analysis (Built-in)
         ↓
Phase 6: Risk Analyzer (Discovery-Specific)
         ↓
    Audit Report
    (Markdown, JSON, or Interactive)
```

**Review Mode Sub-Skill Flow:**

```
Phase 1: Framework Detection
         ↓
Phase 2: Context Research
         ↓
Phase 3: Impact Analyzer (Review-Specific)
         ↓
Phase 4: Breaking Change Detector (Review-Specific)
         ↓
Phase 5: Pre-Flight Validator (Review-Specific)
         ↓
Phase 6: Safety Verifier (Review-Specific)
         ↓
    Risk Report
    (Markdown, JSON, or Interactive)
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
   - **Discovery mode:** Framework Detector → Context Researcher → Architecture Detector → Pattern Identifier → Tech Stack Analyzer → Risk Analyzer
   - **Review mode:** Impact Analyzer → Breaking Change Detector → Pre-flight Validator
   - **Optimization mode:** Performance Profiler → Security Scanner → Efficiency Analyzer
   - **Implementation Planning mode:** Issue Analyzer → Framework Detector → Context Researcher → Implementation Planner → Surgical Prompt Generator
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
