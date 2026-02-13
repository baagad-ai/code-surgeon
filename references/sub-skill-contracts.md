# Sub-Skill Invocation Contracts

## Overview

This document defines the input/output schemas for all 8 code-surgeon sub-skills. These contracts prevent hallucination by providing strict JSON schemas for what each sub-skill expects as input and what it guarantees as output.

---

## Sub-Skills Index

### Core Sub-Skills (Always Available)
1. **framework-detector** - Detects tech stack, languages, versions
2. **context-researcher** - Analyzes codebase, finds relevant files
3. **issue-analyzer** - Parses GitHub issues or plain text requirements
4. **implementation-planner** - Creates 6-section implementation plans
5. **surgical-prompt-generator** - Generates precise code change prompts

### Specialist Sub-Skills (Loaded per Mode)
6. **architecture-analyzer** (Discovery mode) - Deep architecture analysis
7. **risk-assessor** (Review mode) - Risk assessment, breaking change analysis
8. **security-scanner** (Optimization mode) - Vulnerability and performance scanning

---

# CORE SUB-SKILLS

---

## Sub-Skill: framework-detector

### When Loaded
- **Always available** - Executes as Subagent 1B in Phase 1
- **Modes:** QUICK, STANDARD, DEEP
- **Execution:** Parallel with issue-analyzer

### Input Contract (JSON Schema)

```typescript
interface FrameworkDetectorInput {
  // Repository location
  repo_root: string;  // absolute path to repository root, required

  // Configuration
  timeout_ms?: number;  // default: 120000 (2 minutes)

  // Expected output format
  expected_schema?: "FrameworkDetection";
}
```

**Validation Rules:**
- ✓ `repo_root` must be an absolute path that exists
- ✓ `repo_root` must be readable by the process
- ✓ `timeout_ms` must be between 1000 and 300000
- ✓ Must complete within timeout
- ❌ Don't pass relative paths
- ❌ Don't pass non-existent paths

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface FrameworkDetection {
  // Primary language and framework
  primary_language: "javascript" | "python" | "go" | "java" | "rust" | "ruby" | "c#" | "other";
  primary_framework: string | null;  // e.g., "React", "Django", "Express"

  // Detected frameworks
  frameworks: Array<{
    name: string;  // e.g., "React", "Express", "Django"
    version: string;  // e.g., "18.0.0", "4.18.0"
    language: string;  // language this framework is for
    category: "frontend" | "backend" | "database" | "testing" | "utility" | "other";
  }>;

  // Language breakdown
  languages: Array<{
    language: string;  // e.g., "javascript", "typescript", "python"
    file_count: number;  // number of files in this language
    percentage: number;  // percentage of total files (0-100)
  }>;

  // Monorepo detection
  is_monorepo: boolean;
  monorepo_type?: "lerna" | "turbo" | "pnpm" | "gradle" | "go-workspaces" | null;
  workspaces?: string[];  // list of workspace paths

  // Capability flags
  has_typescript: boolean;
  has_testing: boolean;
  has_documentation: boolean;

  // Confidence (0-1)
  confidence: number;

  // Detection metadata
  detection_details: {
    package_manager: string;  // e.g., "npm", "pip", "cargo"
    package_file: string | null;  // e.g., "package.json", "pyproject.toml"
    files_scanned: number;
    detection_time_ms: number;
  };
}
```

### Error Contract (JSON Schema on FAILURE)

```typescript
interface FrameworkDetectorError {
  status: "error";
  error_code: "REPO_NOT_FOUND" | "REPO_UNREADABLE" | "TIMEOUT" | "INVALID_INPUT" | "UNKNOWN";
  message: string;  // human-readable error message
  details: {
    repo_root?: string;
    timeout_ms?: number;
    scanned_files?: number;
    partial_results?: Partial<FrameworkDetection>;  // what was found before error
  };
}
```

### Validation Rules

- ✓ `primary_language` must be one of the supported languages
- ✓ `frameworks[]` must be non-empty array (or empty if no framework detected)
- ✓ `languages[]` must sum to 100% (±1% for rounding)
- ✓ `confidence` must be 0-1
- ✓ `files_scanned` must be > 0
- ✓ `detection_time_ms` must be < timeout_ms
- ❌ Don't include undetected frameworks
- ❌ Don't fabricate version numbers

### Example Usage

**Input:**
```json
{
  "repo_root": "/path/to/myapp",
  "timeout_ms": 120000
}
```

**Output (Success - React + Node.js):**
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
    {
      "language": "typescript",
      "file_count": 145,
      "percentage": 85
    },
    {
      "language": "javascript",
      "file_count": 25,
      "percentage": 15
    }
  ],
  "is_monorepo": false,
  "monorepo_type": null,
  "workspaces": [],
  "has_typescript": true,
  "has_testing": true,
  "has_documentation": true,
  "confidence": 0.96,
  "detection_details": {
    "package_manager": "npm",
    "package_file": "package.json",
    "files_scanned": 170,
    "detection_time_ms": 245
  }
}
```

**Output (Success - Django):**
```json
{
  "primary_language": "python",
  "primary_framework": "Django",
  "frameworks": [
    {
      "name": "Django",
      "version": "4.2.0",
      "language": "python",
      "category": "backend"
    },
    {
      "name": "Django REST Framework",
      "version": "3.14.0",
      "language": "python",
      "category": "backend"
    }
  ],
  "languages": [
    {
      "language": "python",
      "file_count": 156,
      "percentage": 100
    }
  ],
  "is_monorepo": false,
  "monorepo_type": null,
  "workspaces": [],
  "has_typescript": false,
  "has_testing": true,
  "has_documentation": true,
  "confidence": 0.98,
  "detection_details": {
    "package_manager": "pip",
    "package_file": "pyproject.toml",
    "files_scanned": 156,
    "detection_time_ms": 180
  }
}
```

**Output (Error - Repo not found):**
```json
{
  "status": "error",
  "error_code": "REPO_NOT_FOUND",
  "message": "Repository not found at /nonexistent/path",
  "details": {
    "repo_root": "/nonexistent/path"
  }
}
```

### Retry Logic

**If detection fails:**
1. Verify `repo_root` path exists and is readable
2. Check if repository has any recognizable files
3. Retry once with increased timeout (if first attempt timed out)
4. If still failing, proceed with partial results or safe defaults

**If confidence is low (<0.7):**
- Mark as uncertain in output
- Proceed with best guess
- Document assumption for context researcher

### Token Budget

- **Input tokens:** ~100-200 (small fixed input)
- **Output tokens:** ~500-1000 (framework list, language breakdown)
- **Total per execution:** ~1K tokens
- **Budget impact:** Negligible (1% of typical 100K token budget)

### Performance Targets

- **Execution time:** <500ms per repository
- **Accuracy:** 98%+ framework detection, 99%+ language detection
- **Memory:** <5MB per execution

---

## Sub-Skill: context-researcher

### When Loaded
- **Always available** - Executes in Phase 2
- **Modes:** QUICK (30K tokens), STANDARD (60K tokens), DEEP (90K tokens)
- **Execution:** Sequential after framework-detector and issue-analyzer

### Input Contract (JSON Schema)

```typescript
interface ContextResearcherInput {
  // Issue information
  issue_type: "feature" | "bug" | "refactor" | "performance" | "docs";
  requirements: string[];  // 1-20 requirements
  file_hints?: string[];  // paths mentioned in issue, optional
  deadline?: string;  // optional deadline info

  // Framework information (from framework-detector)
  primary_language: string;
  frameworks: Array<{name: string; version: string; language: string; category: string}>;
  is_monorepo: boolean;
  monorepo_type?: string;

  // Configuration
  repo_root: string;  // absolute path to repo root
  depth_mode: "quick" | "standard" | "deep";
  timeout_seconds?: number;  // default: 300 (5 minutes)
}
```

**Validation Rules:**
- ✓ `requirements[]` must be non-empty, max 20 items
- ✓ `issue_type` must be one of 5 types
- ✓ `primary_language` must match framework-detector output
- ✓ `repo_root` must be absolute, readable path
- ✓ `depth_mode` must be one of 3 options
- ✓ `timeout_seconds` must be 60-300 (1-5 minutes)
- ❌ Don't skip `repo_root`
- ❌ Don't pass empty `requirements[]`

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface ContextResearcherOutput {
  // Selected files
  files_selected: Array<{
    path: string;  // relative or absolute path
    tier: 1 | 2 | 3;  // 1=direct impact, 2=dependent, 3=pattern
    size_bytes: number;
    relevance: "critical" | "high" | "medium" | "low";
    reason: string;  // why this file was selected
  }>;

  // File statistics
  file_count: {
    tier_1: number;  // direct impact files
    tier_2: number;  // dependent files
    tier_3: number;  // pattern files
    total: number;
  };

  // Dependency relationships
  dependency_graph: Record<string, {
    imports: string[];  // files this file imports
    exported: string[];  // what this file exports
    imported_by: string[];  // files that import this file
    impact: "critical" | "high" | "medium" | "low";
  }>;

  // Architectural patterns found
  patterns_found: Array<{
    name: string;  // e.g., "React Hook Pattern"
    example_file: string;  // file demonstrating pattern
    description: string;  // pattern description
    location: string;  // where pattern is found (e.g., "src/hooks/**/*.ts")
  }>;

  // Team standards
  team_conventions: string[];  // list of conventions found

  // Token analysis
  token_analysis: {
    tier_1_tokens: number;
    tier_2_tokens: number;
    tier_3_tokens: number;
    total_used: number;
    budget_remaining: number;
    cache_savings: string;  // e.g., "25% (7.5K tokens saved)"
  };

  // Cache status
  cache_status: {
    file_structure_cached: boolean;
    dependency_graph_cached: boolean;
    patterns_cached: boolean;
    cache_created?: string;  // ISO 8601 timestamp
  };

  // Metadata
  analysis_metadata: {
    depth_mode: "quick" | "standard" | "deep";
    files_scanned: number;
    duration_seconds: number;
    primary_language: string;
    is_monorepo: boolean;
  };
}
```

### Error Contract (JSON Schema on FAILURE)

```typescript
interface ContextResearcherError {
  status: "error";
  error_code: "REPO_NOT_FOUND" | "ANALYSIS_TIMEOUT" | "TOKEN_OVERFLOW" | "UNKNOWN";
  message: string;
  details: {
    partial_results?: Partial<ContextResearcherOutput>;
    recommendation?: string;  // e.g., "Switch to QUICK mode"
  };
}
```

### Validation Rules

- ✓ `files_selected[]` must be non-empty
- ✓ `file_count.total` must equal `files_selected.length`
- ✓ `file_count` tier numbers must sum to total
- ✓ `tier` must be 1, 2, or 3
- ✓ `relevance` must be one of 4 levels
- ✓ `token_analysis.total_used` must be <= depth_mode budget
- ✓ `analysis_metadata.duration_seconds` must be <= timeout_seconds
- ✓ All file paths must exist in repository
- ❌ Don't include non-existent files
- ❌ Don't exceed token budget
- ❌ Don't include circular import chains without marking them

### Example Usage

**Input (Feature request - Dark mode):**
```json
{
  "issue_type": "feature",
  "requirements": [
    "Detect system preference using prefers-color-scheme",
    "Allow manual toggle in settings",
    "Store preference in localStorage",
    "Apply theme to all pages"
  ],
  "file_hints": [],
  "primary_language": "typescript",
  "frameworks": [{"name": "React", "version": "18.2.0", "language": "typescript", "category": "frontend"}],
  "is_monorepo": false,
  "repo_root": "/path/to/myapp",
  "depth_mode": "standard",
  "timeout_seconds": 300
}
```

**Output (Success):**
```json
{
  "files_selected": [
    {
      "path": "src/context/ThemeContext.tsx",
      "tier": 1,
      "size_bytes": 2400,
      "relevance": "critical",
      "reason": "Core theme context, needs modification for toggle integration"
    },
    {
      "path": "src/hooks/useLocalStorage.ts",
      "tier": 3,
      "size_bytes": 1200,
      "relevance": "high",
      "reason": "Pattern example for localStorage persistence"
    },
    {
      "path": "src/App.tsx",
      "tier": 2,
      "size_bytes": 3100,
      "relevance": "high",
      "reason": "Root component, will wrap with theme provider"
    }
  ],
  "file_count": {
    "tier_1": 4,
    "tier_2": 8,
    "tier_3": 3,
    "total": 15
  },
  "dependency_graph": {
    "src/context/ThemeContext.tsx": {
      "imports": ["react"],
      "exported": ["ThemeProvider", "useTheme"],
      "imported_by": ["src/App.tsx", "src/pages/Settings.tsx"],
      "impact": "critical"
    },
    "src/hooks/useLocalStorage.ts": {
      "imports": ["react"],
      "exported": ["useLocalStorage"],
      "imported_by": ["src/utils/storage.ts", "src/hooks/useAuth.ts"],
      "impact": "high"
    }
  },
  "patterns_found": [
    {
      "name": "React Context Pattern",
      "example_file": "src/context/ThemeContext.tsx",
      "description": "Global state management using React Context API",
      "location": "src/context/**/*.tsx"
    },
    {
      "name": "Custom Hook Pattern",
      "example_file": "src/hooks/useLocalStorage.ts",
      "description": "Custom hooks for side effects and state management",
      "location": "src/hooks/**/*.ts"
    }
  ],
  "team_conventions": [
    "Use TypeScript strict mode",
    "Functional components only",
    "Use hooks for state management",
    "All exports documented with JSDoc"
  ],
  "token_analysis": {
    "tier_1_tokens": 8000,
    "tier_2_tokens": 15000,
    "tier_3_tokens": 5000,
    "total_used": 28000,
    "budget_remaining": 32000,
    "cache_savings": "30% (8K tokens saved from cache)"
  },
  "cache_status": {
    "file_structure_cached": true,
    "dependency_graph_cached": false,
    "patterns_cached": true,
    "cache_created": "2025-02-13T10:15:00Z"
  },
  "analysis_metadata": {
    "depth_mode": "standard",
    "files_scanned": 185,
    "duration_seconds": 145,
    "primary_language": "typescript",
    "is_monorepo": false
  }
}
```

**Output (Error - Token overflow):**
```json
{
  "status": "error",
  "error_code": "TOKEN_OVERFLOW",
  "message": "Selected files would exceed token budget",
  "details": {
    "partial_results": {
      "file_count": {"tier_1": 12, "tier_2": 45, "tier_3": 8, "total": 65},
      "token_analysis": {"total_used": 65000, "budget_remaining": 0}
    },
    "recommendation": "Switch to QUICK mode or reduce scope"
  }
}
```

### Retry Logic

**If analysis times out:**
1. Return partial results collected so far
2. Recommend switching to QUICK mode
3. Mark as "incomplete" in output

**If token budget exceeded:**
1. Remove lowest-confidence Tier 2 files
2. Remove all Tier 3 except top 3 patterns
3. Retry within budget
4. If still over, return error with recommendation

### Token Budget

- **Input tokens:** ~200-500 (requirements, frameworks)
- **Processing:** Analysis consumes up to depth_mode budget (30K-90K)
- **Output tokens:** ~2K-5K (selected files, analysis)
- **Total per execution:** Varies by depth (30K-95K total)

### Performance Targets

- **QUICK mode:** <2 minutes, ~25K tokens used
- **STANDARD mode:** <5 minutes, ~50K tokens used
- **DEEP mode:** <10 minutes, ~80K tokens used
- **Accuracy:** 95%+ file selection match with manual review

---

## Sub-Skill: issue-analyzer

### When Loaded
- **Always available** - Executes as Subagent 1A in Phase 1
- **Modes:** QUICK, STANDARD, DEEP
- **Execution:** Parallel with framework-detector

### Input Contract (JSON Schema)

```typescript
interface IssueAnalyzerInput {
  // Issue source
  requirement: string;  // GitHub URL or plain text requirement

  // Configuration
  timeout_seconds?: number;  // default: 120 (2 minutes)
}
```

**Validation Rules:**
- ✓ `requirement` must be non-empty string, max 10,000 characters
- ✓ If URL: must start with "https://github.com"
- ✓ If URL: must be valid GitHub issue format
- ✓ `timeout_seconds` must be 30-120
- ❌ Don't accept empty strings
- ❌ Don't accept non-GitHub URLs
- ❌ Don't accept malformed GitHub URLs

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface IssueAnalysis {
  // Source information
  source: "github" | "plain_text";

  // Parsed issue details
  title: string;  // 1-100 characters
  description: string;  // full issue description

  // Classification
  issue_type: "feature" | "bug" | "refactor" | "performance" | "docs";

  // Parsed requirements
  requirements: string[];  // 1-20 requirements extracted

  // Optional metadata
  deadline?: string;  // e.g., "end of week", "urgent", null
  file_hints?: string[];  // paths mentioned in issue

  // Scope estimation
  scope: "small" | "medium" | "large";

  // Confidence in classification (0-1)
  confidence: number;

  // Source metadata
  metadata: {
    source_url?: string;  // GitHub URL if applicable
    labels?: string[];  // GitHub labels if applicable
    priority?: string;  // Priority if found
  };
}
```

### Error Contract (JSON Schema on FAILURE)

```typescript
interface IssueAnalyzerError {
  status: "error";
  error_code: "INVALID_URL" | "GITHUB_API_ERROR" | "EMPTY_INPUT" | "TIMEOUT" | "UNKNOWN";
  message: string;
  details: {
    input_length?: number;
    expected_format?: string;
    suggestion?: string;
  };
}
```

### Validation Rules

- ✓ `title` must be 1-100 characters
- ✓ `requirements[]` must be 1-20 items
- ✓ `issue_type` must be one of 5 types
- ✓ `scope` must be small/medium/large
- ✓ `confidence` must be 0-1
- ✓ `scope` must match requirements count: 1-5→small, 6-12→medium, 13+→large
- ❌ Don't include fabricated requirements
- ❌ Don't assume type if confidence < 0.6
- ❌ Don't fabricate GitHub data

### Example Usage

**Input (GitHub URL):**
```json
{
  "requirement": "https://github.com/myorg/myapp/issues/234",
  "timeout_seconds": 120
}
```

**Output (Success - Feature request):**
```json
{
  "source": "github",
  "title": "Add JWT token refresh mechanism",
  "description": "Our authentication system currently uses fixed 24-hour tokens. This causes frequent logouts for long-running sessions.",
  "issue_type": "feature",
  "requirements": [
    "Create /api/auth/refresh endpoint",
    "Implement sliding window expiration",
    "Add refresh token rotation",
    "Update AuthContext to use refresh endpoint"
  ],
  "deadline": null,
  "file_hints": [
    "src/auth/authContext.tsx",
    "src/api/auth.ts",
    "src/types/auth.d.ts"
  ],
  "scope": "medium",
  "confidence": 0.98,
  "metadata": {
    "source_url": "https://github.com/myorg/myapp/issues/234",
    "labels": ["enhancement", "auth"],
    "priority": null
  }
}
```

**Input (Plain text):**
```json
{
  "requirement": "Add dark mode support. Users have requested a dark theme. The app should: 1) Detect system preference using prefers-color-scheme, 2) Allow manual toggle in settings, 3) Store preference in localStorage, 4) Apply theme to all pages. Target: Complete by EOW",
  "timeout_seconds": 120
}
```

**Output (Success - Feature, plain text):**
```json
{
  "source": "plain_text",
  "title": "Add dark mode support",
  "description": "Users have requested a dark theme for nighttime use. The app should detect system preference, allow manual toggle, store preference, and apply globally.",
  "issue_type": "feature",
  "requirements": [
    "Detect system preference using prefers-color-scheme",
    "Allow manual toggle in settings",
    "Store preference in localStorage",
    "Apply theme to all pages"
  ],
  "deadline": "end of week",
  "file_hints": [],
  "scope": "medium",
  "confidence": 0.92,
  "metadata": {
    "source_url": null,
    "labels": [],
    "priority": null
  }
}
```

**Output (Error - Invalid URL):**
```json
{
  "status": "error",
  "error_code": "INVALID_URL",
  "message": "Invalid GitHub URL. Must be in format: github.com/org/repo/issues/number",
  "details": {
    "input_length": 35,
    "expected_format": "https://github.com/org/repo/issues/number",
    "suggestion": "Provide a valid GitHub issue URL or plain text requirement"
  }
}
```

### Retry Logic

**If GitHub API fails:**
1. Retry once with exponential backoff (1 second wait)
2. If still fails, return error with suggestion to use plain text

**If type confidence low (<0.6):**
1. Score all types
2. Include confidence in output
3. Implementation planner can request clarification

**If requirements not clear:**
1. Use full description as single requirement
2. Mark with low confidence
3. Continue processing

### Token Budget

- **Input tokens:** ~100-300 (URL or requirement text)
- **GitHub API call:** ~500 tokens (if URL)
- **Output tokens:** ~500-1000 (analysis)
- **Total per execution:** ~1K-2K tokens

### Performance Targets

- **Plain text:** <500ms
- **GitHub URL:** <1.5 seconds (includes API call + retries)
- **Accuracy:** 90%+ type classification, 95%+ requirement extraction

---

## Sub-Skill: implementation-planner

### When Loaded
- **Always available** - Executes as Subagent 4 in Phase 4
- **Modes:** QUICK, STANDARD, DEEP
- **Execution:** Sequential after all prior phases complete

### Input Contract (JSON Schema)

```typescript
interface ImplementationPlannerInput {
  // From issue-analyzer
  issue_type: "feature" | "bug" | "refactor" | "performance" | "docs";
  requirements: string[];
  scope: "small" | "medium" | "large";
  deadline?: string;

  // From framework-detector
  primary_language: string;
  primary_framework: string | null;
  frameworks: Array<{name: string; version: string; language: string; category: string}>;
  is_monorepo: boolean;

  // From context-researcher
  files_selected: Array<{path: string; tier: number; content?: string}>;
  dependency_graph: Record<string, {imports: string[]; exported: string[]; imported_by: string[]}>;
  patterns_found: Array<{name: string; description: string; example_file: string}>;
  team_conventions: string[];

  // Configuration
  repo_root: string;
  depth_mode: "quick" | "standard" | "deep";
  timeout_seconds?: number;  // default: 600 (10 minutes)
}
```

**Validation Rules:**
- ✓ All required fields must be present
- ✓ `requirements[]` must match from issue-analyzer
- ✓ `scope` must match calculated from requirements count
- ✓ `files_selected[]` from context-researcher must be non-empty
- ✓ `depth_mode` must be one of 3 options
- ✓ `timeout_seconds` must be 60-600
- ❌ Don't modify input data
- ❌ Don't skip validation checks

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface ImplementationPlan {
  // Metadata
  plan_id: string;  // UUID or similar unique ID
  title: string;
  created_at: string;  // ISO 8601 timestamp
  issue_type: "feature" | "bug" | "refactor" | "performance" | "docs";
  scope: "small" | "medium" | "large";

  // Section 1: Summary
  summary: {
    overview: string;  // 2-3 sentence executive summary
    key_challenges: string[];  // 2-5 challenges
    estimated_effort: {
      optimistic_hours: number;
      realistic_hours: number;
      pessimistic_hours: number;
    };
    risk_level: "low" | "medium" | "high";
    risk_factors: string[];
  };

  // Section 2: Research
  research: {
    framework_patterns: Array<{
      name: string;
      description: string;
      reference_files: string[];
      framework: string;
    }>;
    architectural_patterns: Array<{
      name: string;
      applies_here: boolean;
      examples: string[];
    }>;
    related_code: Array<{
      path: string;
      type: "example" | "similar" | "baseline";
      reason: string;
    }>;
  };

  // Section 3: Design Choices
  design_choices: Array<{
    decision: string;
    problem: string;
    options: Array<{
      name: string;
      pros: string[];
      cons: string[];
      effort_impact: string;
    }>;
    recommended: string;
    rationale: string;
    team_convention_alignment: string;
  }>;

  // Section 4: Phases
  phases: Array<{
    phase_id: string;
    number: number;
    name: string;
    description: string;
    task_ids: string[];
    estimated_hours: number;
    success_criteria: string[];
    dependencies: number[];  // phase numbers this depends on
  }>;

  // Section 5: Tasks
  tasks: Array<{
    task_id: string;
    title: string;
    description: string;
    phase_id: string;
    files_affected: string[];
    estimated_hours: number;
    complexity: "simple" | "moderate" | "complex";
    dependencies: string[];  // task IDs this depends on
    success_criteria: string[];
    verification_approach: string;
    team_convention_checks: string[];
  }>;

  // Section 6: Verification
  verification: {
    testing_strategy: string;
    integration_points: Array<{
      after_phase: number;
      validation_steps: string[];
    }>;
    final_validation: string[];
    rollback_procedure: string;
    verification_checklist: string[];
  };

  // Analysis
  analysis: {
    breaking_changes: Array<{
      type: "api" | "data" | "behavior" | "dependency";
      description: string;
      impact_scope: string;
      affected_dependents: string[];
      migration_path: string;
    }>;
    critical_path: {
      task_ids: string[];
      total_hours: number;
      phase_sequence: number[];
    };
    parallelizable_tasks: Array<string[]>;
    assumptions: string[];
    unknowns: string[];
    constraints: string[];
  };
}
```

### Error Contract (JSON Schema on FAILURE)

```typescript
interface ImplementationPlanError {
  status: "error";
  error_code: "NO_VALID_TASKS" | "CIRCULAR_DEPS" | "CONFLICTING_REQUIREMENTS" | "TIMEOUT" | "UNKNOWN";
  message: string;
  details: {
    conflicting_items?: string[];
    circular_dependency_chain?: string[];
    suggestion?: string;
  };
}
```

### Validation Rules

- ✓ `phases[]` must be 1-5 phases
- ✓ `tasks[]` must be 1-30 tasks
- ✓ All tasks must belong to exactly one phase
- ✓ `estimated_effort` must follow: optimistic < realistic < pessimistic
- ✓ `critical_path.total_hours` must be <= sum of realistic_hours
- ✓ All task dependencies must exist
- ✓ No circular task dependencies
- ✓ Each task must have 2-5 success criteria
- ❌ Don't create > 30 tasks for small scope
- ❌ Don't skip breaking change detection
- ❌ Don't create circular dependencies

### Example Usage

**Input (Dark mode feature):**
```json
{
  "issue_type": "feature",
  "requirements": [
    "Detect system preference using prefers-color-scheme",
    "Allow manual toggle in settings",
    "Store preference in localStorage",
    "Apply theme to all pages"
  ],
  "scope": "medium",
  "primary_language": "typescript",
  "primary_framework": "React",
  "frameworks": [{"name": "React", "version": "18.2.0", "language": "typescript", "category": "frontend"}],
  "is_monorepo": false,
  "files_selected": [
    {"path": "src/context/ThemeContext.tsx", "tier": 1},
    {"path": "src/App.tsx", "tier": 2}
  ],
  "dependency_graph": {},
  "patterns_found": [
    {"name": "React Context Pattern", "description": "Global state with context", "example_file": "src/context/ThemeContext.tsx"}
  ],
  "team_conventions": ["Use hooks", "TypeScript strict mode"],
  "repo_root": "/path/to/myapp",
  "depth_mode": "standard"
}
```

**Output (Success):**
```json
{
  "plan_id": "plan-abc123def456",
  "title": "Add dark mode support with system preference detection",
  "created_at": "2025-02-13T10:30:00Z",
  "issue_type": "feature",
  "scope": "medium",
  "summary": {
    "overview": "Implement dark mode with automatic system preference detection and manual override. Store user preference in localStorage for persistence across sessions.",
    "key_challenges": [
      "System preference detection across browsers",
      "Global theme application without CSS cascade issues",
      "Persistence with localStorage availability"
    ],
    "estimated_effort": {
      "optimistic_hours": 10,
      "realistic_hours": 14,
      "pessimistic_hours": 20
    },
    "risk_level": "low",
    "risk_factors": [
      "Browser compatibility with prefers-color-scheme",
      "localStorage not available in private browsing"
    ]
  },
  "research": {
    "framework_patterns": [
      {
        "name": "React Context for Global State",
        "description": "Use Context API for theme management",
        "reference_files": ["src/context/ThemeContext.tsx"],
        "framework": "React"
      }
    ],
    "architectural_patterns": [
      {
        "name": "Provider Pattern",
        "applies_here": true,
        "examples": ["src/context/ThemeContext.tsx", "src/context/AuthContext.tsx"]
      }
    ],
    "related_code": [
      {
        "path": "src/hooks/useLocalStorage.ts",
        "type": "example",
        "reason": "Demonstrates localStorage persistence pattern"
      }
    ]
  },
  "design_choices": [
    {
      "decision": "Where to store theme preference?",
      "problem": "User preference must persist across sessions",
      "options": [
        {
          "name": "localStorage",
          "pros": ["Simple", "Browser-native", "No server needed"],
          "cons": ["Not available in private mode", "Needs error handling"],
          "effort_impact": "Low"
        },
        {
          "name": "Server-side database",
          "pros": ["Always available", "Works everywhere"],
          "cons": ["Requires API endpoint", "Additional complexity"],
          "effort_impact": "High"
        }
      ],
      "recommended": "localStorage",
      "rationale": "Simple, user-centric preference not critical to sync",
      "team_convention_alignment": "Matches team preference for client-side state"
    }
  ],
  "phases": [
    {
      "phase_id": "phase-1",
      "number": 1,
      "name": "Theme Infrastructure",
      "description": "Set up theme context and hooks",
      "task_ids": ["T1", "T2", "T3"],
      "estimated_hours": 4,
      "success_criteria": [
        "Theme context created and exported",
        "useTheme hook works",
        "Provider component compiles"
      ],
      "dependencies": []
    },
    {
      "phase_id": "phase-2",
      "number": 2,
      "name": "System Preference Detection",
      "description": "Detect and apply system preference",
      "task_ids": ["T4", "T5"],
      "estimated_hours": 5,
      "success_criteria": [
        "System preference detected on page load",
        "Updates when system preference changes",
        "Applied to DOM"
      ],
      "dependencies": [1]
    },
    {
      "phase_id": "phase-3",
      "number": 3,
      "name": "User Toggle & Persistence",
      "description": "Add toggle UI and localStorage",
      "task_ids": ["T6", "T7"],
      "estimated_hours": 5,
      "success_criteria": [
        "Toggle switches between light/dark",
        "Preference persists across refresh",
        "Settings page shows current preference"
      ],
      "dependencies": [2]
    }
  ],
  "tasks": [
    {
      "task_id": "T1",
      "title": "Create ThemeProvider context",
      "description": "Create React context for theme state management",
      "phase_id": "phase-1",
      "files_affected": ["src/context/ThemeContext.tsx"],
      "estimated_hours": 1,
      "complexity": "simple",
      "dependencies": [],
      "success_criteria": [
        "Context exports ThemeProvider component",
        "Context exports useTheme hook",
        "TypeScript types defined",
        "Tests pass"
      ],
      "verification_approach": "Unit test ThemeContext",
      "team_convention_checks": [
        "TypeScript strict mode",
        "JSDoc on exports"
      ]
    },
    {
      "task_id": "T2",
      "title": "Create useTheme hook",
      "description": "Custom hook to access theme context",
      "phase_id": "phase-1",
      "files_affected": ["src/hooks/useTheme.ts"],
      "estimated_hours": 1,
      "complexity": "simple",
      "dependencies": ["T1"],
      "success_criteria": [
        "Hook returns theme context",
        "Hook validates context availability",
        "Error handling for context missing",
        "Tests pass"
      ],
      "verification_approach": "Unit test useTheme",
      "team_convention_checks": [
        "Hook naming (useXxx)",
        "Error boundary"
      ]
    }
  ],
  "verification": {
    "testing_strategy": "Unit test contexts and hooks, integration test with components, manual browser testing",
    "integration_points": [
      {
        "after_phase": 1,
        "validation_steps": [
          "Context provider wraps App",
          "useTheme hook available to components"
        ]
      },
      {
        "after_phase": 3,
        "validation_steps": [
          "Toggle works",
          "Preference persists",
          "Visual changes apply"
        ]
      }
    ],
    "final_validation": [
      "npm test passes",
      "npm run lint passes",
      "Manual test: Light mode works",
      "Manual test: Dark mode works",
      "Manual test: Preference persists after refresh",
      "Manual test: Works in Safari, Chrome, Firefox"
    ],
    "rollback_procedure": "Revert theme context and hooks, restore App.tsx",
    "verification_checklist": [
      "All tests passing",
      "Code style compliant",
      "Types compile",
      "Manual verification complete"
    ]
  },
  "analysis": {
    "breaking_changes": [],
    "critical_path": {
      "task_ids": ["T1", "T2", "T4", "T5", "T6", "T7"],
      "total_hours": 12,
      "phase_sequence": [1, 2, 3]
    },
    "parallelizable_tasks": [
      ["T1", "T2"],
      ["T4", "T5"]
    ],
    "assumptions": [
      "localStorage available in target browsers",
      "React 18+ available",
      "CSS variables supported"
    ],
    "unknowns": [
      "Exact CSS variable naming for theme colors"
    ],
    "constraints": [
      "Must not break existing functionality",
      "Must work in all supported browsers"
    ]
  }
}
```

### Retry Logic

**If no valid tasks can be derived:**
1. Return error with clarification needed
2. Implementation planner cannot proceed without tasks

**If circular dependencies detected:**
1. Identify circular chain
2. Return error with chain description
3. Ask for clarification on ordering

### Token Budget

- **Input tokens:** ~5K-15K (all prior phase outputs)
- **Planning computation:** Analysis includes tokens
- **Output tokens:** ~3K-8K (plan sections)
- **Total per execution:** ~8K-25K tokens
- **Reserve:** Leaves 10K-25K for Phase 5 (Surgical Prompt Generator)

### Performance Targets

- **Plan generation:** <5 minutes
- **Accuracy:** 90%+ task decomposition, ±25% effort estimation
- **Completeness:** 100% requirements addressed, 100% success criteria per task

---

## Sub-Skill: surgical-prompt-generator

### When Loaded
- **Always available** - Executes as Subagent 5 in Phase 5
- **Modes:** QUICK, STANDARD, DEEP
- **Execution:** Sequential per task from implementation planner

### Input Contract (JSON Schema)

```typescript
interface SurgicalPromptGeneratorInput {
  // Task to implement
  task: {
    task_id: string;
    title: string;
    description: string;
    files_affected: string[];
    success_criteria: string[];
    dependencies: string[];
    verification_approach: string;
  };

  // Context from implementation planner
  design_choices: Array<{
    decision: string;
    recommended: string;
    rationale: string;
  }>;

  breaking_changes: Array<{
    type: string;
    description: string;
    migration_path: string;
  }>;

  // Context from phases 1-3
  framework: string;
  language: string;
  patterns_found: Array<{name: string; description: string; example_file: string}>;
  team_conventions: string[];
  files_selected: Array<{path: string; tier: number; content?: string}>;

  // Configuration
  repo_root: string;
  depth_mode: "quick" | "standard" | "deep";
  token_budget?: number;  // remaining tokens, default: 15000
}
```

**Validation Rules:**
- ✓ `task` must be complete with all required fields
- ✓ `files_affected[]` must be non-empty
- ✓ `success_criteria[]` must be 2-5 items
- ✓ `framework` and `language` must match context
- ✓ `token_budget` must be 5000-50000
- ❌ Don't skip validation
- ❌ Don't generate without task definition

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface SurgicalPrompt {
  // Metadata
  prompt_id: string;  // UUID or similar unique ID
  task_id: string;
  task_title: string;
  framework: string;
  language: string;
  token_estimate: number;

  // 9 Sections (breaking_changes optional if not applicable)
  sections: {
    // Section 1: Objective
    objective: string;  // 1-2 sentences, crystal clear goal

    // Section 2: Context
    context: string;  // 2-3 sentences, why this matters

    // Section 3: Scope
    scope: {
      files_to_modify: string[];  // files to create/edit
      files_to_reference: string[];  // files to read as reference
      files_to_avoid: string[];  // do NOT modify these files
    };

    // Section 4: Approach
    approach: string;  // Framework-specific how-to (2-4 paragraphs)

    // Section 5: Patterns
    patterns: Array<{
      name: string;
      source_file: string;  // real file in codebase
      how_to_apply: string;  // how to apply in this task
    }>;

    // Section 6: Constraints
    constraints: Array<{
      category: string;  // "code style", "architecture", "testing", "documentation"
      rules: string[];
    }>;

    // Section 7: Breaking Changes (if applicable)
    breaking_changes?: Array<{
      change: string;
      impact: string;
      migration_path: string;
    }>;

    // Section 8: Success Criteria
    success_criteria: string[];  // verifiable, testable criteria

    // Section 9: Common Mistakes
    common_mistakes: Array<{
      mistake: string;
      solution: string;
    }>;
  };

  // Verification
  verification: Array<{
    step: string;
    expected_result: string;
  }>;

  // Dependencies
  task_dependencies: string[];
  blocks_tasks: string[];
}
```

### Error Contract (JSON Schema on FAILURE)

```typescript
interface SurgicalPromptError {
  status: "error";
  error_code: "INVALID_TASK" | "TOKEN_OVERFLOW" | "MISSING_CONTEXT" | "UNKNOWN";
  message: string;
  details: {
    required_field?: string;
    estimated_tokens?: number;
    recommendation?: string;
  };
}
```

### Validation Rules

- ✓ `objective` must be 1-2 sentences
- ✓ `context` must be 2-3 sentences
- ✓ `files_to_modify[]` must match task.files_affected
- ✓ All referenced files must exist in repository
- ✓ `patterns[]` must be 2-4 items
- ✓ `constraints[]` must group rules by category
- ✓ `success_criteria[]` must match task success_criteria
- ✓ `common_mistakes[]` must be 3-5 items
- ✓ `token_estimate` must be < token_budget
- ❌ Don't generate generic prompts
- ❌ Don't include non-existent files
- ❌ Don't exceed token budget
- ❌ Don't skip any section

### Example Usage

**Input (Dark mode toggle component):**
```json
{
  "task": {
    "task_id": "T1",
    "title": "Create ThemeProvider context",
    "description": "Create React context for theme state management",
    "files_affected": ["src/context/ThemeContext.tsx"],
    "success_criteria": [
      "Context exports ThemeProvider component",
      "Context exports useTheme hook",
      "TypeScript types defined",
      "Tests pass"
    ],
    "dependencies": [],
    "verification_approach": "Unit test ThemeContext"
  },
  "design_choices": [
    {
      "decision": "Where to store theme preference",
      "recommended": "localStorage",
      "rationale": "Simple, user-centric, no server needed"
    }
  ],
  "breaking_changes": [],
  "framework": "React",
  "language": "typescript",
  "patterns_found": [
    {
      "name": "React Context Pattern",
      "description": "Global state with Context API",
      "example_file": "src/context/AuthContext.tsx"
    }
  ],
  "team_conventions": [
    "TypeScript strict mode",
    "Functional components only",
    "JSDoc on exports"
  ],
  "files_selected": [
    {"path": "src/context/AuthContext.tsx", "tier": 3, "content": "..."}
  ],
  "repo_root": "/path/to/myapp",
  "depth_mode": "standard",
  "token_budget": 12000
}
```

**Output (Success):**
```json
{
  "prompt_id": "prompt-xyz789abc123",
  "task_id": "T1",
  "task_title": "Create ThemeProvider context",
  "framework": "React",
  "language": "typescript",
  "token_estimate": 8500,
  "sections": {
    "objective": "Create a React Context for managing application theme state (light/dark), exporting both a ThemeProvider component and useTheme hook for accessing theme in any component.",

    "context": "This context forms the foundation for dark mode support. Phase 1 sets up the state management infrastructure that Phase 2 (system preference detection) and Phase 3 (toggle UI) will build upon. Getting the context structure right is critical—this task unblocks the rest of the feature.",

    "scope": {
      "files_to_modify": [
        "src/context/ThemeContext.tsx"
      ],
      "files_to_reference": [
        "src/context/AuthContext.tsx",
        "src/hooks/useAuth.ts"
      ],
      "files_to_avoid": [
        "src/App.tsx",
        "All component files",
        "All style files"
      ]
    },

    "approach": "This uses React Context API for global state management, following the same pattern your codebase uses in AuthContext. Create a context with theme state, then export a custom hook (useTheme) for consuming the context. Use TypeScript interfaces for the theme object and context type. The context should support light/dark modes and provide a way for downstream code to update the theme (we'll use that in Phase 3 for the toggle). No external dependencies needed—use only React built-ins.",

    "patterns": [
      {
        "name": "React Context Pattern",
        "source_file": "src/context/AuthContext.tsx",
        "how_to_apply": "Follow the same structure: createContext, provider component, custom hook. Create interface for context value, wrap Provider with TypeScript generics."
      },
      {
        "name": "Custom Hook Pattern",
        "source_file": "src/hooks/useAuth.ts",
        "how_to_apply": "Similar pattern: hook uses useContext, validates context exists, returns context value. Add error handling if context not found."
      }
    ],

    "constraints": [
      {
        "category": "Code Style",
        "rules": [
          "All files must be TypeScript (.tsx for components, .ts for hooks)",
          "Use functional components only",
          "PascalCase for components (ThemeProvider), camelCase for hooks (useTheme)",
          "Use const for exports: export const ThemeProvider = ...",
          "No default exports"
        ]
      },
      {
        "category": "TypeScript",
        "rules": [
          "Must compile with strict mode (no any)",
          "Define interface for theme object: { mode: 'light' | 'dark' }",
          "Type the context value: { theme: Theme; setTheme?: (theme: Theme) => void }",
          "Generic type on createContext: createContext<ThemeContextType | undefined>(undefined)"
        ]
      },
      {
        "category": "Testing",
        "rules": [
          "Write unit tests for ThemeContext",
          "Test that ThemeProvider renders without error",
          "Test that useTheme returns correct context value",
          "Test that useTheme throws if used outside provider",
          "Use React Testing Library"
        ]
      },
      {
        "category": "Documentation",
        "rules": [
          "Add JSDoc comments to all exports",
          "Document ThemeProvider props and behavior",
          "Document useTheme hook return type",
          "Add example usage in JSDoc"
        ]
      }
    ],

    "success_criteria": [
      "✓ File src/context/ThemeContext.tsx created",
      "✓ ThemeProvider component exported",
      "✓ useTheme hook exported",
      "✓ TypeScript compiles without errors",
      "✓ Tests pass: npm test ThemeContext",
      "✓ Linting passes: npm run lint",
      "✓ JSDoc comments on all exports"
    ],

    "common_mistakes": [
      {
        "mistake": "Not defining proper TypeScript types for context",
        "solution": "Define interface ThemeContextType = { theme: Theme; setTheme: (theme: Theme) => void }. Then createContext<ThemeContextType | undefined>(undefined) with proper generics."
      },
      {
        "mistake": "Using default export instead of named exports",
        "solution": "Use: export const ThemeProvider = ... and export const useTheme = ..., not export default"
      },
      {
        "mistake": "Forgetting to handle context being undefined in useTheme",
        "solution": "Add check: const context = useContext(ThemeContext); if (!context) throw new Error('useTheme must be used within ThemeProvider')"
      },
      {
        "mistake": "Creating new functions on every render",
        "solution": "Use useCallback for setTheme: const setTheme = useCallback((theme) => { ... }, [])"
      }
    ]
  },

  "verification": [
    {
      "step": "Run: npm test ThemeContext",
      "expected_result": "All tests pass"
    },
    {
      "step": "Run: npm run type-check",
      "expected_result": "No TypeScript errors"
    },
    {
      "step": "Run: npm run lint",
      "expected_result": "No linting errors"
    },
    {
      "step": "Verify ThemeProvider can wrap components",
      "expected_result": "No runtime errors when provider wraps component"
    },
    {
      "step": "Verify useTheme hook works",
      "expected_result": "Hook returns theme object correctly"
    }
  ],

  "task_dependencies": [],
  "blocks_tasks": ["T2", "T4", "T5"]
}
```

**Output (Error - Token overflow):**
```json
{
  "status": "error",
  "error_code": "TOKEN_OVERFLOW",
  "message": "Surgical prompt for this task would exceed token budget",
  "details": {
    "estimated_tokens": 18500,
    "token_budget": 12000,
    "recommendation": "Break task into smaller sub-tasks or increase token budget from Phase 5 allocation"
  }
}
```

### Retry Logic

**If token budget exceeded:**
1. Remove lowest-value sections
2. Reduce code examples
3. Return error with recommendation

**If context incomplete:**
1. Return error with missing fields
2. Ask for clarification
3. Cannot proceed without complete task definition

### Token Budget

- **Per-task prompts:** 5K-15K tokens each
- **Total Phase 5 budget:** 15K-25K (from total allocation)
- **Number of tasks:** Typically 3-10 tasks per plan
- **Budget management:** Each task gets: remaining_budget / remaining_tasks tokens

### Performance Targets

- **Per-prompt generation:** <1 minute
- **Batch (5-10 tasks):** <10 minutes
- **Token accuracy:** 100% adherence to budget
- **Clarity:** 95%+ clarity score

---

# SPECIALIST SUB-SKILLS

---

## Sub-Skill: architecture-analyzer

### When Loaded
- **Discovery mode only** - Phase 3 of discovery workflow
- **Modes:** QUICK (limited), STANDARD (full), DEEP (comprehensive)
- **Execution:** Sequential after framework-detector and context-researcher

### Input Contract (JSON Schema)

```typescript
interface ArchitectureAnalyzerInput {
  // From prior phases
  primary_language: string;
  frameworks: Array<{name: string; version: string; category: string}>;
  files_selected: Array<{path: string; tier: number}>;
  dependency_graph: Record<string, {imports: string[]; imported_by: string[]}>;
  patterns_found: Array<{name: string; location: string}>;

  // Configuration
  repo_root: string;
  depth_mode: "quick" | "standard" | "deep";
  timeout_seconds?: number;  // default: 300
}
```

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface ArchitectureAnalysis {
  // Architecture overview
  architecture_type: string;  // "Layered MVC", "Microservices", "Monolithic", etc.

  // Module breakdown
  modules: Array<{
    name: string;
    files: string[];
    responsibilities: string;
    dependencies: string[];
    impact: "critical" | "high" | "medium" | "low";
  }>;

  // Data flow
  data_flow: string[];  // data flow paths through system

  // System boundaries
  boundaries: {
    client_server?: string;
    server_database?: string;
    [key: string]: string | undefined;
  };

  // Quality metrics
  metrics: {
    module_count: number;
    max_dependency_depth: number;
    circular_dependencies: string[];
    modularity_score: number;  // 0-100
  };
}
```

### Validation Rules

- ✓ All modules must have responsibilities documented
- ✓ Data flow must be realistic
- ✓ No undefined module references
- ✓ Circular dependencies explicitly noted if found
- ✓ Modularity score must be 0-100

### Example Output (Django project)

```json
{
  "architecture_type": "Layered MVC",
  "modules": [
    {
      "name": "Authentication",
      "files": ["accounts/models.py", "accounts/views.py", "accounts/serializers.py"],
      "responsibilities": "User login, JWT validation, session management",
      "dependencies": ["core/utils", "core/permissions"],
      "impact": "critical"
    },
    {
      "name": "API Layer",
      "files": ["api/views.py", "api/urls.py", "api/permissions.py"],
      "responsibilities": "HTTP endpoints, request validation, response formatting",
      "dependencies": ["accounts", "services"],
      "impact": "high"
    }
  ],
  "data_flow": [
    "Request → API Layer → Services → Models → Database",
    "Response ← API Layer ← Services ← Database"
  ],
  "boundaries": {
    "client_server": "api/views.py (REST endpoints)",
    "server_database": "core/models.py (ORM)"
  },
  "metrics": {
    "module_count": 5,
    "max_dependency_depth": 3,
    "circular_dependencies": [],
    "modularity_score": 78
  }
}
```

### Token Budget

- **Input tokens:** ~1K-2K
- **Output tokens:** ~2K-4K
- **Total:** ~3K-6K tokens

---

## Sub-Skill: risk-assessor

### When Loaded
- **Review mode only** - Phase 4 of review workflow
- **Modes:** QUICK (direct), STANDARD (direct + transitive), DEEP (comprehensive)
- **Execution:** Sequential after framework-detector and context-researcher

### Input Contract (JSON Schema)

```typescript
interface RiskAssessorInput {
  // Proposed change description
  change_description: string;
  change_type: "api" | "data" | "behavior" | "dependency" | "other";

  // From prior phases
  files_selected: Array<{path: string; tier: number}>;
  dependency_graph: Record<string, {imports: string[]; imported_by: string[]}>;

  // Configuration
  repo_root: string;
  depth_mode: "quick" | "standard" | "deep";
  timeout_seconds?: number;  // default: 300
}
```

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface RiskAssessment {
  // Overall assessment
  risk_level: "critical" | "high" | "medium" | "low";
  overall_summary: string;

  // Breaking changes detected
  breaking_changes: Array<{
    type: string;
    severity: "critical" | "high" | "medium" | "low";
    description: string;
    affected_files: string[];
    migration_effort: string;
  }>;

  // Impact analysis
  impact_analysis: {
    direct_dependents: number;
    transitive_dependents: number;
    affected_modules: string[];
    affected_tests: number;
  };

  // Pre-flight checklist
  preflight_checklist: Array<{
    item: string;
    status: "required" | "recommended" | "optional";
    why: string;
  }>;

  // Safety verification
  safety_verification: {
    data_loss_risk: "none" | "low" | "medium" | "high";
    reversibility: string;
    error_handling_coverage: number;  // percentage
    test_coverage: number;  // percentage
  };
}
```

### Validation Rules

- ✓ All breaking changes must have migration paths
- ✓ Impact numbers must be realistic
- ✓ Checklist items must be actionable
- ✓ Safety scores must be 0-100%

### Example Output

```json
{
  "risk_level": "high",
  "overall_summary": "Renaming core authentication function breaks 3 direct dependents and affects 8 transitive. Requires comprehensive testing and migration.",
  "breaking_changes": [
    {
      "type": "api",
      "severity": "critical",
      "description": "authenticate() function signature changed",
      "affected_files": ["src/api/auth.ts", "src/middleware/auth.ts"],
      "migration_effort": "30 minutes"
    }
  ],
  "impact_analysis": {
    "direct_dependents": 3,
    "transitive_dependents": 8,
    "affected_modules": ["API", "Middleware", "Services"],
    "affected_tests": 12
  },
  "preflight_checklist": [
    {
      "item": "Run full test suite",
      "status": "required",
      "why": "12 tests affected by this change"
    }
  ],
  "safety_verification": {
    "data_loss_risk": "none",
    "reversibility": "yes - can revert to old signature with deprecation",
    "error_handling_coverage": 95,
    "test_coverage": 92
  }
}
```

### Token Budget

- **Input tokens:** ~1K-2K
- **Output tokens:** ~2K-4K
- **Total:** ~3K-6K tokens

---

## Sub-Skill: security-scanner

### When Loaded
- **Optimization mode only** - Phase 4 of optimization workflow
- **Modes:** QUICK (critical), STANDARD (all), DEEP (with remediation code)
- **Execution:** Sequential after framework-detector and context-researcher

### Input Contract (JSON Schema)

```typescript
interface SecurityScannerInput {
  // From prior phases
  files_selected: Array<{path: string; tier: number; content?: string}>;
  primary_language: string;
  frameworks: Array<{name: string; version: string}>;

  // Configuration
  repo_root: string;
  depth_mode: "quick" | "standard" | "deep";
  scan_type?: "vulnerabilities" | "dependencies" | "input_validation" | "authentication" | "all";
  timeout_seconds?: number;  // default: 300
}
```

### Output Contract (JSON Schema on SUCCESS)

```typescript
interface SecurityScan {
  // Vulnerability summary
  total_vulnerabilities: number;
  critical_count: number;
  high_count: number;
  medium_count: number;
  low_count: number;

  // Vulnerabilities found
  vulnerabilities: Array<{
    severity: "critical" | "high" | "medium" | "low";
    type: string;  // "sql_injection", "xss", "command_injection", etc.
    location: string;  // file path and line number
    description: string;
    impact: string;
    remediation: string;
    code_before?: string;  // current vulnerable code
    code_after?: string;  // fixed code
    effort_minutes?: number;
  }>;

  // Dependency analysis
  dependency_vulnerabilities: Array<{
    package: string;
    current_version: string;
    vulnerable_to: string;
    fix_version: string;
    severity: "critical" | "high" | "medium" | "low";
  }>;

  // Recommendations
  prioritized_fixes: Array<{
    rank: number;
    issue: string;
    effort_minutes: number;
    impact_score: number;  // 1-10
    roi: number;  // impact/effort
  }>;
}
```

### Validation Rules

- ✓ All vulnerabilities must have location, description, remediation
- ✓ Severity must be one of 4 levels
- ✓ ROI must be > 0 (impact/effort)
- ✓ Prioritized fixes must be sorted by ROI

### Example Output

```json
{
  "total_vulnerabilities": 3,
  "critical_count": 1,
  "high_count": 1,
  "medium_count": 1,
  "low_count": 0,
  "vulnerabilities": [
    {
      "severity": "critical",
      "type": "sql_injection",
      "location": "src/db/queries.ts:45",
      "description": "User input concatenated directly into SQL query",
      "impact": "Database compromise, data leak, potential remote code execution",
      "remediation": "Use parameterized queries or prepared statements",
      "code_before": "const query = `SELECT * FROM users WHERE name = '${userName}'`;",
      "code_after": "const query = 'SELECT * FROM users WHERE name = ?'; db.query(query, [userName]);",
      "effort_minutes": 15
    },
    {
      "severity": "high",
      "type": "xss",
      "location": "src/components/UserProfile.tsx:45",
      "description": "User-generated content rendered as HTML without sanitization",
      "impact": "Malicious scripts can execute in user browser, steal sessions",
      "remediation": "Remove dangerouslySetInnerHTML or use DOMPurify library",
      "code_before": "<div>{dangerouslySetInnerHTML={{__html: userBio}}}</div>",
      "code_after": "<div>{userBio}</div>  // React escapes by default",
      "effort_minutes": 10
    }
  ],
  "dependency_vulnerabilities": [
    {
      "package": "lodash",
      "current_version": "4.17.19",
      "vulnerable_to": "Prototype pollution (CVE-2021-23337)",
      "fix_version": "4.17.21",
      "severity": "high"
    }
  ],
  "prioritized_fixes": [
    {
      "rank": 1,
      "issue": "SQL injection in user query",
      "effort_minutes": 15,
      "impact_score": 10,
      "roi": 0.67
    },
    {
      "rank": 2,
      "issue": "Upgrade lodash to 4.17.21",
      "effort_minutes": 5,
      "impact_score": 8,
      "roi": 1.6
    }
  ]
}
```

### Token Budget

- **Input tokens:** ~2K-5K
- **Output tokens:** ~3K-6K
- **Total:** ~5K-11K tokens

---

# INTEGRATION SUMMARY

## Data Flow

```
Phase 1:
  framework-detector → {primary_language, frameworks[], is_monorepo}
  issue-analyzer → {issue_type, requirements[], scope}
  ↓
Phase 2-3:
  context-researcher → {files_selected[], dependency_graph, patterns[]}
  ↓
Phase 4:
  implementation-planner → {phases[], tasks[], breaking_changes[]}
  ↓
Phase 5:
  surgical-prompt-generator (per task) → {prompt sections...}
  ↓
Phase 6+ (Optional):
  architecture-analyzer (Discovery) → {architecture analysis}
  risk-assessor (Review) → {risk assessment, breaking changes}
  security-scanner (Optimization) → {vulnerabilities, recommendations}
```

## Mode-to-Specialist Mapping

| Mode | Specialist Used | When | Purpose |
|------|-----------------|------|---------|
| **Discovery** | architecture-analyzer | Phase 3 | Deep architecture understanding |
| **Review** | risk-assessor | Phase 4 | Pre-change risk assessment |
| **Optimization** | security-scanner | Phase 4 | Vulnerability & performance scanning |

## Token Budgets by Mode

| Mode | Total | Phases 1-3 | Phase 4 (Planner) | Phase 5 (Prompts) | Specialist |
|------|-------|-----------|------------------|------------------|-----------|
| QUICK | 30K | 15K | 5K | 8K | 2K |
| STANDARD | 60K | 35K | 10K | 12K | 3K |
| DEEP | 90K | 55K | 15K | 15K | 5K |

---

# ERROR RECOVERY MATRIX

| Error | Sub-Skill | Recovery Action |
|-------|-----------|-----------------|
| Repo not found | framework-detector | Return error, stop pipeline |
| Timeout | context-researcher | Return partial results, recommend QUICK mode |
| Token overflow | implementation-planner | Remove Tier 3 patterns, retry |
| Invalid URL | issue-analyzer | Retry with retries, suggest plain text |
| Circular deps | implementation-planner | Return error, request clarification |
| No tasks derived | implementation-planner | Return error, stop pipeline |

---

# VALIDATION CHECKLIST

When invoking any sub-skill:

- [ ] Input validates against Input Contract schema
- [ ] Required fields present and non-empty
- [ ] Types match expected schema
- [ ] File paths exist and readable (if repo access needed)
- [ ] Timeout reasonable for task
- [ ] Token budget sufficient for task + output

When receiving output:

- [ ] Status is "success" or "error"
- [ ] Output validates against Output Contract schema
- [ ] All required fields present
- [ ] No unexpected data types
- [ ] Token count within budget
- [ ] Completion within timeout

---

# TESTING SUB-SKILLS

Each sub-skill is tested with:

1. **Happy path:** Valid input, produces expected output
2. **Error path:** Invalid input, produces proper error
3. **Edge cases:** Boundary conditions, large inputs, minimal inputs
4. **Integration:** Output from one feeds input to next
5. **Token budgets:** Verify no overflow, accurate estimates
6. **Performance:** Verify within timeout targets

See `TDD_TEST_SPECIFICATION.md` for complete test cases.
