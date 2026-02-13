# Discovery Mode

Understand unfamiliar codebases through systematic architecture and pattern analysis.

---

## When to Use Discovery Mode

Use Discovery mode when you need to **understand** a codebase without a specific change in mind.

### Indicators Discovery is Right

- Unfamiliar codebase (new team member, inherited project)
- Need architecture overview before planning
- Need to identify architectural patterns
- Need tech stack assessment
- Need security/risk audit
- Understanding existing behavior before modifying

### Example Use Cases

1. **Onboarding new team member:** "Help me understand how this system works"
2. **Code audit:** "What are the security risks in this codebase?"
3. **Architecture assessment:** "What patterns does this project use?"
4. **Pre-refactoring understanding:** "Before we refactor, I need to understand the current system"
5. **System investigation:** "How does the payment flow work?"
6. **Technical debt assessment:** "What's the technical debt in this codebase?"

---

## Discovery Workflow (Step-by-Step)

### Phase 1: Framework Detection (2 minutes)

**What happens:**
- Scan package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml
- Detect frameworks, versions, language(s), monorepo status

**Output:**
```json
{
  "frameworks": ["react", "express", ...],
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
- Analyze codebase structure using regex patterns
- Build lightweight dependency graph
- Extract architectural patterns
- Find team conventions from `.claude/team-guidelines.md`

**Output:**
```json
{
  "files": {
    "tier_1": ["src/auth.ts", "src/api.ts"],
    "tier_2": ["src/hooks/useAuth.ts", "src/middleware.ts"],
    "tier_3": ["src/utils.ts"]
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
  }
}
```

---

### Phase 3: Architecture Detection (3 minutes)

**What happens:**
1. **Map system architecture:**
   - Identify core modules and subsystems
   - Find inter-module dependencies
   - Detect data flow patterns
   - Identify client/server/database boundaries

2. **Detect architectural style:**
   - Monolithic vs microservices
   - MVC / MVVM / CQRS / Event-driven
   - Layered / Hexagonal / Pipe-and-filter
   - Server-side rendering vs client-side

**Output:**
```json
{
  "architecture_type": "Layered MVC",
  "modules": [
    {
      "name": "Authentication",
      "files": ["src/auth/", "src/middleware/auth.ts"],
      "responsibilities": "User login, JWT validation, session management",
      "dependencies": ["src/utils/", "src/db/"]
    },
    {
      "name": "API",
      "files": ["src/api/", "src/routes/"],
      "responsibilities": "HTTP endpoints, request validation",
      "dependencies": ["src/auth/", "src/services/"]
    }
  ],
  "data_flow": [
    "Request → API → Auth → Service → Database",
    "Response ← API ← Service ← Database"
  ],
  "boundaries": {
    "client_server": "src/api/ (Express endpoints)",
    "server_database": "src/db/ (ORM queries)"
  }
}
```

---

### Phase 4: Pattern Identification (3 minutes)

**What happens:**
1. **Identify architectural patterns:**
   - Design patterns (Singleton, Factory, Observer, etc.)
   - Architectural patterns (MVC, layering, etc.)
   - Communication patterns (RPC, publish-subscribe, etc.)

2. **Find framework-specific patterns:**
   - React: Custom hooks, render patterns, state management
   - Django: Model design, view patterns, middleware
   - Express: Middleware chains, routing patterns
   - Rails: Model conventions, controller patterns

3. **Extract implementation patterns:**
   - Error handling approaches
   - Testing patterns
   - Configuration management
   - Logging/monitoring

**Output:**
```json
{
  "patterns": [
    {
      "type": "Design Pattern",
      "name": "Singleton",
      "location": "src/services/Database.ts",
      "description": "Database connection pool",
      "impact": "Single shared database instance across app"
    },
    {
      "type": "Architectural Pattern",
      "name": "Custom Hook Pattern (React)",
      "locations": ["src/hooks/useAuth.ts", "src/hooks/useFetch.ts"],
      "description": "Encapsulates state logic in reusable hooks",
      "impact": "Consistent state management across components"
    },
    {
      "type": "Communication Pattern",
      "name": "REST API",
      "location": "src/api/",
      "description": "HTTP-based API with standard CRUD endpoints",
      "impact": "Standard HTTP communication with clients"
    }
  ]
}
```

---

### Phase 5: Tech Stack Analysis (2 minutes)

**What happens:**
1. **List all significant dependencies:**
   - Frameworks and libraries
   - Databases and ORMs
   - Testing and build tools
   - Monitoring and logging

2. **Assess tech stack quality:**
   - Version freshness (up-to-date vs outdated)
   - Maintenance status (active vs deprecated)
   - Community size and support
   - Compatibility concerns

**Output:**
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

---

### Phase 6: Risk Identification (2 minutes)

**What happens:**
1. **Security risks:**
   - Hardcoded secrets
   - SQL injection vulnerabilities
   - Cross-site scripting (XSS) risks
   - Authentication/authorization gaps

2. **Technical risks:**
   - Outdated dependencies with known vulnerabilities
   - Performance bottlenecks
   - Scalability concerns
   - Single points of failure

3. **Architectural risks:**
   - Tight coupling
   - Missing error handling
   - Incomplete validation
   - Missing logging/monitoring

**Output:**
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
  ]
}
```

---

## Output Format: Audit Report

Discovery mode generates a comprehensive **Audit Report** in Markdown format:

```markdown
# Code Audit Report

## Overview
[High-level summary of codebase]

## Tech Stack
[Framework versions, dependencies, quality assessment]

## Architecture
[System design, modules, data flow]

## Patterns
[Design patterns, architectural patterns, framework patterns]

## Key Findings
[Important observations about code organization]

## Risks
[Security, technical, architectural risks]

## Recommendations
[Prioritized improvements and next steps]

## Code Metrics
[File counts, complexity, coverage if available]
```

---

## Depth Mode Behavior

### QUICK Mode Discovery
**Duration:** 5 minutes | **Tokens:** ~30K | **Accuracy:** 85%

**What's included:**
- Framework detection
- Basic architecture map (modules only, no detailed flow)
- 2-3 major patterns
- High-risk items only
- Tech stack overview

**What's skipped:**
- Detailed dependency analysis
- Pattern implementation examples
- Minor risks
- Historical analysis
- Performance analysis

---

### STANDARD Mode Discovery
**Duration:** 15 minutes | **Tokens:** ~60K | **Accuracy:** 95%

**What's included:**
- Full framework detection
- Complete architecture with data flow
- 5-10 patterns with examples
- All security risks
- All technical risks
- Complete tech stack assessment
- Vulnerability recommendations

**Output formats:** Markdown (audit report), JSON (structured data)

---

### DEEP Mode Discovery
**Duration:** 30 minutes | **Tokens:** ~90K | **Accuracy:** 99%

**What's included:**
- Everything in STANDARD, plus:
- File history and change frequency
- Detailed dependency graph with version history
- Full pattern implementation examples (10-15 lines per pattern)
- Performance metrics and bottleneck analysis
- Security audit with specific remediation code
- Historical trends and technical debt timeline
- Alternative architecture suggestions

**Output formats:** Markdown (detailed audit), JSON (comprehensive data), Interactive (visual navigation)

---

## Examples of Discovery Use Cases

### Example 1: Onboarding New Team Member

**User request:** `"I just joined the team. Help me understand this codebase."`

**Command:** `/code-surgeon --mode=discovery --depth=STANDARD`

**Output highlights:**
- Architecture overview (modules, dependencies)
- Tech stack explanation
- 5-10 key patterns
- Major risks
- Recommendations for learning path

**What the report tells them:**
- How the system is organized
- What frameworks and libraries are used
- Common patterns they'll see in code
- Things to be careful about
- What to learn first

---

### Example 2: Security Audit

**User request:** `"Audit our codebase for security issues."`

**Command:** `/code-surgeon --mode=discovery --depth=DEEP`

**Output highlights:**
- Hardcoded secrets scan
- Dependency vulnerability check
- Authentication/authorization assessment
- Input validation review
- SQL injection risk analysis

**What the report tells them:**
- Specific security vulnerabilities (with line numbers)
- Severity of each issue
- How to fix each issue
- Risk exposure
- Compliance concerns

---

### Example 3: Architecture Understanding

**User request:** `"I need to understand how the payment flow works."`

**Command:** `/code-surgeon --mode=discovery --depth=STANDARD`

**Output highlights:**
- Payment module architecture
- Data flow from request to database
- Integration points
- Key patterns used
- Error handling approach

**What the report tells them:**
- How payments are processed
- What files to read
- What patterns to understand
- Potential issues
- Dependencies involved

---

### Example 4: Technical Debt Assessment

**User request:** `"What's the technical debt in this codebase?"`

**Command:** `/code-surgeon --mode=discovery --depth=DEEP`

**Output highlights:**
- Outdated dependencies
- Code complexity hotspots
- Missing error handling
- Performance bottlenecks
- Architectural improvements

**What the report tells them:**
- What's breaking down
- Impact of technical debt
- Prioritization for fixes
- Effort to fix each item
- Recommendations

---

## Common Discovery Scenarios

### Scenario: "Assess a new acquisition"
```bash
# First: Broad overview
/code-surgeon --mode=discovery --depth=QUICK

# Then: Deep dive on architecture
/code-surgeon --mode=discovery --depth=DEEP

# Result: Understand if integration is feasible
```

### Scenario: "Security audit before production"
```bash
# Run deep discovery focused on risks
/code-surgeon --mode=discovery --depth=DEEP

# Review all HIGH/CRITICAL risks
# Create remediation plan from risks section
```

### Scenario: "Onboard 3 new engineers"
```bash
# Generate discovery audit
/code-surgeon --mode=discovery --depth=STANDARD

# Share audit report with team
# Use as foundation for code review training
```

### Scenario: "Pre-refactoring analysis"
```bash
# Understand current state
/code-surgeon --mode=discovery --depth=STANDARD

# Then plan refactoring
/code-surgeon "Refactor [module]" --mode=implementation
```

---

## Safety Verification

Discovery mode includes automatic verification:

- ✅ No code modifications (read-only)
- ✅ No external API calls (local analysis only)
- ✅ PII/secret detection in code (warnings, not blocking)
- ✅ Path traversal protection (stays within repo)
- ✅ No bias toward specific technologies

---

## Next Steps After Discovery

1. **Review the audit report**
   - Read overview, architecture, risks
   - Identify patterns you need to understand
   - Note high-risk items

2. **Share with team**
   - Use as onboarding material
   - Discuss risks as team
   - Create remediation roadmap

3. **Plan next action**
   - If you found a specific task: Use Implementation Planning
   - If you found risks: Use Review mode for specific changes
   - If you want optimization: Use Optimization mode
