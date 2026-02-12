# Architecture & How It Works

## 5-Phase Pipeline

code-surgeon operates as a sequential pipeline, with state saved after each phase for resumable sessions.

```
User Input (GitHub issue or requirement)
    ↓
┌──────────────────────────────────────────┐
│ PHASE 1: Analysis (2 minutes)            │
│ ├─ Issue Analyzer: Parse requirements   │
│ ├─ Framework Detector: Identify tech    │
│ └─ Output: Type, requirements, frameworks│
└──────────────────────────────────────────┘
    ↓ [State Saved: Phase 1 Complete]
┌──────────────────────────────────────────┐
│ PHASE 2: Context Research (5 minutes)    │
│ ├─ Analyze file structure               │
│ ├─ Build dependency graph               │
│ ├─ Extract patterns                     │
│ ├─ Find team guidelines                 │
│ └─ Smart file selection (3-tier)        │
└──────────────────────────────────────────┘
    ↓ [State Saved: Phase 2 Complete]
┌──────────────────────────────────────────┐
│ PHASE 3: Planning (3 minutes)            │
│ ├─ Generate 6-section plan              │
│ ├─ Analyze breaking changes             │
│ ├─ Order tasks logically                │
│ └─ Estimate effort                      │
└──────────────────────────────────────────┘
    ↓ [State Saved: Phase 3 Complete]
┌──────────────────────────────────────────┐
│ PHASE 4: Surgical Prompts (2 minutes)    │
│ ├─ Create 9-section prompts per task    │
│ ├─ Apply framework-specific guidance    │
│ ├─ Validate against team guidelines     │
│ └─ Scan for PII/secrets                 │
└──────────────────────────────────────────┘
    ↓ [State Saved: Phase 4 Complete]
┌──────────────────────────────────────────┐
│ PHASE 5: Output Formatting (1 minute)    │
│ ├─ Generate PLAN.md (human-readable)    │
│ ├─ Generate plan.json (machine-readable)│
│ └─ Generate interactive.json (CLI mode) │
└──────────────────────────────────────────┘
    ↓
Three Ready-to-Use Output Files
```

---

## Sub-Skill Architecture

code-surgeon orchestrates 5 specialized sub-skills:

### Phase 1A: Issue Analyzer
**Role:** Parse requirements and detect issue type

**Input:** GitHub URL or plain text description
**Output:**
```json
{
  "type": "feature" | "bug" | "refactor" | "perf",
  "requirements": ["req1", "req2", ...],
  "deadline": "2025-02-20",
  "file_hints": ["src/auth.ts", ...]
}
```

### Phase 1B: Framework Detector
**Role:** Identify frameworks, versions, and tech stack

**Scans:** package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml, etc.
**Output:**
```json
{
  "frameworks": ["react", "express", ...],
  "primary_language": "typescript",
  "versions": {"react": "18.2.0", ...},
  "is_monorepo": true,
  "monorepo_info": {...}
}
```

### Phase 2: Context Researcher
**Role:** Analyze codebase and intelligently select files

**Algorithm: 3-Tier File Selection**
```
Tier 1 (Direct): Files directly mentioned in requirements
Tier 2 (Dependencies): Files imported by Tier 1
Tier 3 (Patterns): Files matching architectural patterns
```

**Token Budget Aware:**
- QUICK: 30K tokens (Tier 1 only)
- STANDARD: 60K tokens (Tier 1 + 2)
- DEEP: 90K tokens (Tier 1 + 2 + 3)

**Output:**
```json
{
  "files": {
    "tier_1": [...],
    "tier_2": [...],
    "tier_3": [...]
  },
  "dependency_graph": {...},
  "patterns": [...],
  "team_conventions": {...}
}
```

### Phase 3: Implementation Planner
**Role:** Create comprehensive 6-section implementation plan

**6 Sections:**
1. Summary (strategy overview)
2. Research (codebase findings)
3. Design Choices (decisions + rationale)
4. Phases (logical work chunks)
5. Tasks (granular work items + dependencies)
6. Verification (testing checklist)

**Breaking Change Detection:**
- API breaking changes
- Data schema breaking changes
- Behavior breaking changes
- Dependency breaking changes

### Phase 4: Surgical Prompt Generator
**Role:** Create precise, actionable prompts per task

**9-Section Prompt Structure:**
1. Objective
2. Context
3. Scope
4. Approach
5. Patterns
6. Constraints
7. Breaking Changes
8. Success Criteria
9. Common Mistakes

**Framework-Specific:**
- React: Hook patterns, component typing
- Django: Model patterns, view patterns
- Express: Middleware, routing patterns
- Rails: Model-View-Controller patterns
- Etc. for 35+ frameworks

---

## State Management

### Session Structure
```
.claude/planning/sessions/surgeon-20250212-abc123xyz/
├── state.json                    ← Complete state (resumable)
├── PLAN.md                       ← Human-readable plan
├── plan.json                     ← Machine-readable plan
├── interactive.json              ← CLI mode data
└── logs/
    └── execution.log             ← Detailed logs
```

### Atomic Saves
- After **each phase completes**, state is saved atomically
- If interrupted, session is recoverable from last completed phase
- Resume command: `/code-surgeon-resume surgeon-20250212-abc123xyz`

### Caching
```
.claude/planning/cache/
├── file-structure-<hash>.json    ← Repo structure
├── dependency-graph-<hash>.json  ← Dependency map
└── patterns-<hash>.json          ← Pattern extraction
```

Caches save 25-30% of tokens on repeated analyses in same repo.

---

## Depth Mode Implementation

### QUICK Mode (5 min)
- Phase 1: Normal
- Phase 2: Skip Tier 3 pattern extraction
- Phase 3: Reduce to 2-4 tasks
- Phase 4: 5-section prompts
- Phase 5: Markdown only

### STANDARD Mode (15 min) - DEFAULT
- Phase 1: Normal
- Phase 2: Load Tier 1 + 2 + 3, extract 3-5 patterns
- Phase 3: Full 6-section plan, 5-8 tasks
- Phase 4: Full 9-section prompts
- Phase 5: All 3 output formats

### DEEP Mode (30 min)
- Phase 1: Normal
- Phase 2: Include file history, full dependency graph, 5-10 patterns
- Phase 3: 2-3 alternatives per decision, comprehensive breaking change analysis
- Phase 4: Extended prompts with 10-15 line code examples
- Phase 5: All 3 output formats with extended context

---

## Error Handling & Recovery

### 7 Error Scenarios

| Scenario | Detection | Recovery | Result |
|----------|-----------|----------|--------|
| Empty requirement | Validation | Show error, stop | User provides requirement |
| Sub-skill timeout | Phase execution | Retry once, then save | User resumes |
| Token budget exceeded | Phase 2-3 | Offer options | User chooses action |
| PII/secrets detected | Phase 4 validation | Block generation | User sanitizes code |
| Output validation fails | Phase output | Retry once | User resumes or restarts |
| Repo not found | Phase 2 init | Show error, stop | User fixes path |
| User interrupt | Any time | Save state immediately | User resumes |

### Resume Protocol
1. User interrupts or error occurs
2. State is saved atomically
3. Session ID provided: `surgeon-<date>-<random>`
4. User can resume: `/code-surgeon-resume surgeon-<id>`
5. System loads state, finds highest completed phase
6. Continues from next phase, reuses prior outputs

---

## Team Guidelines Integration

### Loading Mechanism
```
1. Check if .claude/team-guidelines.md exists
2. If found: Load and parse guidelines
3. If not found: Continue without guidelines
4. Apply guidelines to:
   - Surgical prompt generation
   - Breaking change detection
   - Code example generation
5. Flag violations in output
```

### Guidelines Format
```markdown
# Team Guidelines

## Code Style
- [language-specific rules]

## Architecture Patterns
- [patterns team uses]

## Security & Compliance
- [requirements]
```

---

## Framework Detection & Adaptation

### Detection Algorithm
1. Scan package manager files (package.json, pyproject.toml, etc.)
2. Parse dependencies and versions
3. Match against known framework signatures
4. Determine primary language
5. Check for monorepo indicators

### Framework-Specific Adaptation
```
if framework == "React":
  - Use Hook patterns in examples
  - Reference React best practices
  - Include TypeScript patterns if detected

if framework == "Django":
  - Use Model-View patterns
  - Reference Django ORM
  - Include migration guidance

[Similar logic for 35+ frameworks]
```

---

## Token Efficiency

### Design Principles
1. **Expert knowledge first** — 82% of content is expert-only
2. **Minimal activation content** — Reminders kept brief
3. **No redundancy** — Never explain what Claude already knows
4. **Smart layering** — Load only what's needed per phase
5. **Caching** — Avoid re-analyzing same codebase

### Real-World Numbers
```
Average invocation:
├─ SKILL.md loading: ~12K tokens (once per session)
├─ Phase execution: ~8K tokens (current phase only)
├─ User context: ~2-3K tokens
└─ Total context: ~22K tokens of ~100K available

Result: 78% context window free for work
```

---

## Performance Characteristics

| Phase | Time | Tokens | CPU |
|-------|------|--------|-----|
| Phase 1 | 2 min | ~4K | Low |
| Phase 2 | 5 min | ~12K | Medium |
| Phase 3 | 3 min | ~8K | Medium |
| Phase 4 | 2 min | ~6K | Medium |
| Phase 5 | 1 min | ~2K | Low |
| **Total** | **13 min** | **~32K** | **Medium** |

(Varies by depth mode and codebase size)

---

## Extension Points

Future enhancements:
- Custom framework templates
- Plugin system for specialized analysis
- Integration with CI/CD pipelines
- Advanced pattern detection
- Machine learning-based risk assessment

See [README.md](../README.md) for user-facing documentation.
