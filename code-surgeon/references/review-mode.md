# Review Mode

Assess the impact and safety of planned changes before implementing them.

---

## When to Use Review Mode

Use Review mode when you need to **assess the impact** of a change before implementing it.

### Indicators Review is Right

- Need to understand what breaks before implementing
- Pre-merge safety validation
- Dependency upgrade impact assessment
- Refactoring scope analysis
- Architecture change risk assessment
- API or contract change analysis

### Example Use Cases

1. **Dependency upgrade:** "Is it safe to upgrade React to 18?"
2. **Refactoring scope:** "What breaks if I rename this module?"
3. **API change:** "What clients depend on this endpoint?"
4. **Migration planning:** "What's the impact of moving to TypeScript?"
5. **Pre-merge review:** "Should we merge this refactoring?"
6. **Architecture change:** "Is it safe to move from RabbitMQ to Kafka?"

---

## Review Workflow (Step-by-Step)

### Phase 1: Framework Detection (2 minutes)

**What happens:**
- Scan package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml
- Detect frameworks, versions, language(s), monorepo status

**Output:**
```json
{
  "frameworks": ["react", "express"],
  "primary_language": "typescript",
  "versions": {
    "react": "18.2.0",
    "express": "4.18.2"
  },
  "is_monorepo": false
}
```

---

### Phase 2: Context Research (5 minutes)

**What happens:**
- Analyze codebase structure
- Build dependency graph (what depends on what)
- Extract patterns and conventions
- Find existing breaking change patterns

**Output:**
```json
{
  "files": {
    "tier_1": ["src/auth.ts", "src/api.ts"],
    "tier_2": ["src/hooks/useAuth.ts"],
    "tier_3": ["src/middleware.ts"]
  },
  "dependency_graph": {
    "src/auth.ts": ["src/utils.ts"],
    "src/hooks/useAuth.ts": ["src/auth.ts"],
    "src/api.ts": ["src/auth.ts", "src/hooks/useAuth.ts"]
  },
  "patterns": [...],
  "team_conventions": {...}
}
```

---

### Phase 3: Impact Analysis (3 minutes)

**What happens:**
1. **Map dependencies:**
   - What files depend on the change?
   - What modules are affected (direct + transitive)?
   - What external packages depend on this?

2. **Assess scope:**
   - How many files affected?
   - How many modules affected?
   - How many layers affected (UI, API, DB)?

3. **Identify consumer impact:**
   - External API/package users
   - Internal module consumers
   - Test coverage in affected areas

**Output:**
```json
{
  "scope": {
    "files_affected": 15,
    "modules_affected": 3,
    "layers_affected": ["api", "service", "database"]
  },
  "impact_analysis": {
    "direct_dependents": [
      {
        "file": "src/api/auth.ts",
        "type": "import",
        "impact": "Uses renamed function"
      },
      {
        "file": "src/hooks/useAuth.ts",
        "type": "import",
        "impact": "Uses renamed export"
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

---

### Phase 4: Breaking Change Detection (3 minutes)

**What happens:**
1. **Detect API changes:**
   - Function signature changes (params, return type)
   - Export/import changes (renamed, removed)
   - Endpoint changes (URL, method, body)
   - Data model changes (schema changes)

2. **Detect data changes:**
   - Database schema migrations
   - API response format changes
   - Data type changes
   - Configuration format changes

3. **Detect behavior changes:**
   - Logic modifications
   - Side effect changes
   - Error handling changes
   - Performance implications

4. **Detect dependency changes:**
   - Version bumps (major breaking changes)
   - Removed dependencies
   - New required dependencies
   - Peer dependency conflicts

**Output:**
```json
{
  "breaking_changes": [
    {
      "type": "api",
      "severity": "critical",
      "title": "Function signature changed",
      "location": "src/auth.ts:45",
      "old_signature": "authenticate(username: string, password: string) -> Promise<User>",
      "new_signature": "authenticate(credentials: LoginCredentials) -> Promise<AuthToken>",
      "impact": "All callers must update to new signature",
      "affected_locations": [
        "src/api/auth.ts:20",
        "src/hooks/useAuth.ts:15",
        "tests/auth.test.ts:30"
      ],
      "migration_path": "Update all calls from authenticate(username, password) to authenticate({username, password})",
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
    },
    {
      "type": "behavior",
      "severity": "medium",
      "title": "Error handling changed",
      "location": "src/auth.ts:50",
      "old_behavior": "Throws ValidationError on invalid input",
      "new_behavior": "Returns {error: 'Invalid input'} response",
      "impact": "Calling code expecting exceptions will break",
      "affected_locations": ["src/api/auth.ts", "src/middleware/errorHandler.ts"],
      "migration_path": "Wrap calls in try-catch or check response.error",
      "effort_estimate": "15 minutes"
    }
  ]
}
```

---

### Phase 5: Pre-Flight Validation (2 minutes)

**What happens:**
1. **Validate completeness:**
   - All affected files identified?
   - All tests accounted for?
   - All consumers covered?

2. **Validate migration path:**
   - Is migration reversible?
   - Backward compatibility possible?
   - Deprecation period needed?

3. **Create pre-flight checklist:**
   - Run full test suite?
   - Update documentation?
   - Notify downstream users?
   - Staging environment test?

**Output:**
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
      "why": "Large architectural change"
    },
    "test_coverage": {
      "item": "Run full test suite",
      "status": "pending",
      "why": "15 files modified, 16 tests affected"
    },
    "backward_compatibility": {
      "item": "Check backward compatibility",
      "status": "pending",
      "why": "3 breaking changes detected"
    },
    "documentation": {
      "item": "Update API documentation",
      "status": "pending",
      "why": "Endpoint signature changed"
    },
    "downstream": {
      "item": "Notify npm package users",
      "status": "pending",
      "why": "External package depends on this API"
    },
    "staging": {
      "item": "Test in staging environment",
      "status": "pending",
      "why": "Database migration required"
    }
  }
}
```

---

### Phase 6: Safety Verification (1 minute)

**What happens:**
1. **Verify no data loss:**
   - Database migrations are reversible?
   - Deprecation warning needed?

2. **Verify error handling:**
   - All error cases covered?
   - Clear error messages?

3. **Verify test coverage:**
   - Changed code has tests?
   - Breaking change tests added?

**Output:**
```json
{
  "safety_verification": {
    "data_loss_risk": "low",
    "reversibility": "yes - migration can be rolled back",
    "error_handling": "complete",
    "test_coverage": {
      "changed_lines": 45,
      "covered_lines": 43,
      "coverage_percentage": 95.6
    },
    "recommendation": "Safe to merge with pre-flight checklist completed"
  }
}
```

---

## Output Format: Risk Report

Review mode generates a **Risk Report** in Markdown:

```markdown
# Change Impact Report

## Summary
[Overview of change and impact level]

## Scope
[How many files, modules, layers affected]

## Breaking Changes
[Detailed list of breaking changes by category]

## Impact Analysis
[Direct and transitive dependents]

## Pre-Flight Checklist
[Steps to validate before merging]

## Risk Assessment
[Safety verification and recommendations]

## Migration Path
[How to migrate dependent code]
```

---

## Depth Mode Behavior

### QUICK Mode Review
**Duration:** 5 minutes | **Tokens:** ~30K | **Accuracy:** 85%

**What's included:**
- Direct impact analysis only
- Major breaking changes
- Critical risks
- Basic pre-flight checklist

**What's skipped:**
- Transitive dependencies
- Minor breaking changes
- Detailed migration paths
- Test coverage analysis
- Performance implications

---

### STANDARD Mode Review
**Duration:** 15 minutes | **Tokens:** ~60K | **Accuracy:** 95%

**What's included:**
- Complete impact analysis (direct + transitive)
- All breaking changes (critical + high + medium)
- Complete pre-flight checklist
- Affected test count
- Migration paths for all changes
- Safety verification
- Risk assessment

**Output formats:** Markdown (risk report), JSON (structured data)

---

### DEEP Mode Review
**Duration:** 30 minutes | **Tokens:** ~90K | **Accuracy:** 99%

**What's included:**
- Everything in STANDARD, plus:
- File-by-file impact breakdown
- Detailed dependency graph visualization
- Alternative migration strategies
- Rollback procedures
- Performance impact analysis
- Security impact analysis
- Cost estimation for migration

**Output formats:** Markdown (detailed report), JSON (comprehensive data), Interactive (visual impact map)

---

## Output Formats by Depth

| Depth | Markdown | JSON | Interactive |
|-------|----------|------|-------------|
| **QUICK** | ✅ Risk report (summary) | ❌ | ❌ |
| **STANDARD** | ✅ Risk report (full) | ✅ Structured data | ❌ |
| **DEEP** | ✅ Risk report (exhaustive) | ✅ Structured data | ✅ Step-through CLI |

---

## Risk Assessment Categories

### Critical Risks (Stop and Review)

**Characteristics:** Data loss, security exposure, system outage

**Examples:**
- Data loss: Database schema migration without backward compatibility (users lose data)
- Security: Removing authentication from an endpoint (security breach)
- API break: Changing API response format without versioning (clients break)
- Credential exposure: Hardcoding API keys in code (credential exposure)

**Action:** Do not proceed without explicit risk mitigation.

---

### High Risks (Careful Review)

**Characteristics:** Breaking changes, third-party impacts, non-trivial refactoring

**Examples:**
- Breaking API: Renaming a public function that other teams depend on (API break)
- Type change: Changing return type from object to array (consumer code fails)
- Major bump: Upgrading dependency with major version bump (compatibility issues)
- Required params: Adding required parameters to optional function (breaking change)

**Action:** Document breaking changes and provide migration guide.

---

### Medium Risks (Noted)

**Characteristics:** Moderate impact changes, internal refactoring with external touch points

**Examples:**
- Module move: Moving utility function to different module (import paths change)
- State restructure: Restructuring internal state management (internal but affects output)
- Performance change: Changing performance characteristics (slow vs fast, memory usage)
- Error messages: Modifying error messages (logging systems depend on these)

**Action:** Document changes and notify affected teams.

---

### Low Risks (Proceed)

**Characteristics:** Internal changes, optimizations, documentation updates

**Examples:**
- Internal rename: Refactoring internal variable names
- Perf optimize: Improving performance without changing interface
- Test addition: Adding tests without changing code
- Documentation: Updating comments and documentation

**Action:** Proceed normally, document in commit message.

---

## Examples of Review Use Cases

### Example 1: Dependency Upgrade

**User request:** `"Is it safe to upgrade React from 17 to 18?"`

**Command:** `/code-surgeon "Upgrade React 17 to 18" --mode=review --depth=DEEP`

**Output highlights:**
- Breaking changes in React 18
- Files that will need updates
- Migration path per file
- Test impact
- Performance implications

**Risk assessment:**
- Concurrent features opt-in (safe)
- Hook API unchanged (safe)
- Removed legacy Root API (breaking for legacy code)

---

### Example 2: API Refactoring

**User request:** `"Rename the /auth endpoint to /authentication. Will this break anything?"`

**Command:** `/code-surgeon "/auth → /authentication refactor" --mode=review --depth=STANDARD`

**Output highlights:**
- All API callers identified
- Frontend/mobile impact
- External package dependencies
- Migration checklist

**Breaking changes detected:**
- 3 internal endpoints changed
- 1 external package depends on it
- 4 tests need updates

---

### Example 3: Pre-Merge Review

**User request:** `"Is this refactoring safe to merge?" [points to PR]`

**Command:** `/code-surgeon --mode=review "Large refactoring PR" --depth=DEEP`

**Output highlights:**
- What changed and why
- Safety validation
- Pre-flight checklist
- Risk assessment

**Verdict:**
- Safe to merge IF pre-flight checklist completed
- Document breaking changes in CHANGELOG
- Notify 2 downstream teams

---

### Example 4: Schema Migration

**User request:** `"I'm adding a NOT NULL column. What needs to change?"`

**Command:** `/code-surgeon "Add NOT NULL column to users table" --mode=review --depth=STANDARD`

**Output highlights:**
- Existing code that writes to users table
- Migration strategy
- Tests that need updates
- Rollback procedure

**Risk assessment:**
- Existing rows need default value
- Migration must be reversible
- Coordinate with deployment

---

## Safety Verification Checklist

Before implementing a change with breaking changes:

- ✅ All breaking changes identified
- ✅ Migration paths documented
- ✅ Affected tests identified
- ✅ External users notified
- ✅ Rollback plan exists
- ✅ Deprecation period planned (if applicable)
- ✅ Documentation updated
- ✅ Code reviewed
- ✅ Full test suite passes
- ✅ Staging environment validated

---

## Code Review Patterns

Review mode identifies:

1. **Single Responsibility Principle violations**
   - File doing too much
   - Module overlap

2. **Dependency pattern issues**
   - Circular dependencies
   - Unnecessary dependencies

3. **Breaking change patterns**
   - Unsignaled changes
   - Incompatible upgrades

4. **Error handling patterns**
   - Missing error cases
   - Inconsistent error handling

---

## Common Review Scenarios

### Scenario: "Check before major refactoring"
```bash
# Assess impact first
/code-surgeon "Refactor [module]" --mode=review --depth=DEEP

# Review all breaking changes
# Create migration plan from output
# Then implement with Implementation Planning mode
```

### Scenario: "Validate dependency upgrades"
```bash
# Check what breaks
/code-surgeon "Upgrade [package]" --mode=review --depth=STANDARD

# If breaking changes:
#   - Create migration plan
#   - Implement with care
# If safe:
#   - Proceed with upgrade
```

### Scenario: "Pre-merge validation"
```bash
# Run review mode on PR changes
/code-surgeon "Validate PR #123" --mode=review --depth=STANDARD

# Check pre-flight checklist
# Ensure all items completed before merge
```

### Scenario: "Impact assessment for clients"
```bash
# Deep review to understand full impact
/code-surgeon "Major API change" --mode=review --depth=DEEP

# Share risk report with stakeholders
# Plan migration timeline
# Notify affected teams
```

---

## Next Steps After Review

1. **Review the risk report**
   - Read all breaking changes
   - Understand impact scope
   - Review pre-flight checklist

2. **Make go/no-go decision**
   - If safe: Proceed to implementation
   - If risky: Plan mitigation
   - If too complex: Scope differently

3. **Plan implementation (if proceeding)**
   - Use Implementation Planning mode
   - Reference breaking changes from review report
   - Use pre-flight checklist as validation

4. **Notify affected parties**
   - If external APIs/packages break
   - If major changes
   - If deprecation period needed
