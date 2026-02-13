# Optimization Mode

Identify and prioritize code improvements: performance gains, security hardening, and efficiency improvements.

---

## When to Use Optimization Mode

Use Optimization mode when you want to **improve existing code** without major architectural changes.

### Indicators Optimization is Right

- Need to improve performance
- Need to add security hardening
- Want to reduce technical debt
- Looking for code quality improvements
- Seeking efficiency gains
- Want to simplify complex code

### Example Use Cases

1. **Performance:** "This endpoint is slow, optimize it"
2. **Security:** "Find security vulnerabilities in our API"
3. **Efficiency:** "Reduce bundle size"
4. **Quality:** "This function is too complex, simplify it"
5. **Technical debt:** "What can we improve in this module?"
6. **Resource usage:** "This database query is expensive"

---

## Optimization Workflow (Step-by-Step)

### Phase 1: Framework Detection (2 minutes)

**What happens:**
- Scan package.json, pyproject.toml, go.mod, Gemfile, Cargo.toml
- Detect frameworks, versions, language(s), build system

**Output:**
```json
{
  "frameworks": ["react", "express"],
  "primary_language": "typescript",
  "versions": {
    "react": "18.2.0",
    "express": "4.18.2"
  },
  "build_system": "webpack"
}
```

---

### Phase 2: Context Research (5 minutes)

**What happens:**
- Analyze codebase structure
- Build dependency graph
- Extract patterns and conventions
- Identify performance-sensitive code areas

**Output:**
```json
{
  "files": {
    "tier_1": ["src/api/", "src/database/"],
    "tier_2": ["src/services/"],
    "tier_3": ["src/utils/"]
  },
  "dependency_graph": {...},
  "hotspots": [
    {
      "type": "api_endpoint",
      "location": "src/api/users.ts:45",
      "frequency": "10k requests/day"
    },
    {
      "type": "database_query",
      "location": "src/db/queries.ts:120",
      "frequency": "high"
    }
  ],
  "tech_stack": {...}
}
```

---

### Phase 3: Performance Bottleneck Detection (3 minutes)

**What happens:**
1. **Identify slow operations:**
   - Database queries (N+1 problems, missing indexes)
   - API endpoints (large responses, slow backends)
   - Frontend rendering (re-renders, memory leaks)
   - Build process (slow bundles, large assets)

2. **Detect inefficient patterns:**
   - Unnecessary loops/iterations
   - Redundant computations
   - Memory leaks
   - Cache misses

3. **Assess impact:**
   - User experience impact
   - Resource usage
   - Cost implications

**Output:**
```json
{
  "performance_bottlenecks": [
    {
      "severity": "high",
      "type": "database",
      "location": "src/db/queries.ts:45",
      "issue": "N+1 query problem",
      "description": "Query users, then in loop query posts per user",
      "current_performance": "1000ms for 100 users",
      "impact": "2 HTTP requests per user = 200 queries for 100 users",
      "optimization": "Use JOIN or batch load posts",
      "estimated_improvement": "100ms (10x faster)",
      "effort": "30 minutes",
      "code_before": "for (const user of users) { user.posts = await getPosts(user.id); }",
      "code_after": "const posts = await getPostsByUserIds(users.map(u => u.id));"
    },
    {
      "severity": "medium",
      "type": "frontend",
      "location": "src/components/UserList.tsx:20",
      "issue": "Unnecessary re-renders",
      "description": "Component re-renders on every parent update",
      "current_performance": "50ms per render",
      "impact": "Slow pagination when user list large",
      "optimization": "Wrap in React.memo or useMemo",
      "estimated_improvement": "10ms (5x faster)",
      "effort": "15 minutes"
    },
    {
      "severity": "low",
      "type": "build",
      "location": "webpack.config.js",
      "issue": "Large bundle size",
      "description": "Bundle is 500KB (uncompressed)",
      "impact": "Slower initial page load",
      "optimization": "Code splitting, tree shaking, dynamic imports",
      "estimated_improvement": "50% bundle size reduction",
      "effort": "2 hours"
    }
  ]
}
```

---

### Phase 4: Security Vulnerability Scanning (3 minutes)

**What happens:**
1. **Identify input validation gaps:**
   - SQL injection risks
   - XSS vulnerabilities
   - Command injection risks
   - File traversal risks

2. **Find authentication/authorization issues:**
   - Missing authentication checks
   - Weak password policies
   - Token expiration issues
   - CORS misconfigurations

3. **Detect dependency vulnerabilities:**
   - Known CVEs in dependencies
   - Outdated packages
   - Insecure patterns

**Output:**
```json
{
  "security_vulnerabilities": [
    {
      "severity": "critical",
      "type": "sql_injection",
      "location": "src/db/queries.ts:120",
      "issue": "Unsanitized SQL query",
      "description": "User input concatenated directly into SQL",
      "impact": "Database compromise, data leak",
      "current_code": "const query = `SELECT * FROM users WHERE name = '${userName}'`;",
      "fixed_code": "const query = 'SELECT * FROM users WHERE name = ?'; db.query(query, [userName]);",
      "effort": "15 minutes"
    },
    {
      "severity": "high",
      "type": "xss",
      "location": "src/components/UserProfile.tsx:45",
      "issue": "Unsanitized HTML render",
      "description": "User-generated content rendered as HTML",
      "impact": "Malicious scripts can run in user browser",
      "current_code": "return <div>{dangerouslySetInnerHTML={{__html: userBio}}}</div>;",
      "fixed_code": "return <div>{userBio}</div>;  // React escapes by default",
      "effort": "10 minutes"
    },
    {
      "severity": "medium",
      "type": "dependency",
      "location": "package.json",
      "issue": "Outdated dependency with CVE",
      "description": "lodash 4.17.19 has known vulnerabilities",
      "impact": "Prototype pollution attack possible",
      "current_version": "4.17.19",
      "fixed_version": "4.17.21",
      "effort": "5 minutes (run npm update)"
    }
  ]
}
```

---

### Phase 5: Efficiency Analysis & Recommendations (2 minutes)

**What happens:**
1. **Code quality improvements:**
   - Complexity reduction
   - Dead code elimination
   - Duplication removal
   - Better abstractions

2. **Resource efficiency:**
   - Memory usage
   - CPU usage
   - Disk I/O
   - Network bandwidth

3. **Maintainability improvements:**
   - Code readability
   - Test coverage
   - Documentation
   - Consistency

**Output:**
```json
{
  "efficiency_improvements": [
    {
      "severity": "medium",
      "type": "code_quality",
      "location": "src/utils/helpers.ts",
      "issue": "Complex nested conditionals",
      "description": "Function has 8 nested if-else statements",
      "current_complexity": "cyclomatic complexity = 8",
      "target_complexity": "< 5",
      "optimization": "Use switch statement or early returns",
      "estimated_improvement": "Easier to read and test",
      "effort": "30 minutes"
    },
    {
      "severity": "low",
      "type": "dead_code",
      "location": "src/services/auth.ts:100-150",
      "issue": "Unused function",
      "description": "oldLoginMethod() not called anywhere",
      "impact": "Code maintenance burden",
      "optimization": "Remove dead code",
      "effort": "5 minutes"
    },
    {
      "severity": "medium",
      "type": "duplication",
      "location": "src/components/",
      "issue": "Duplicated component logic",
      "description": "3 similar form components with repeated validation",
      "impact": "Maintenance burden, inconsistency",
      "optimization": "Extract shared validation to hook",
      "effort": "1 hour"
    }
  ]
}
```

---

### Phase 6: Prioritization & Roadmapping (5 minutes)

**What happens:**
Integrate findings from Performance, Security, Efficiency analyses into:

1. **Impact/Effort matrix:**
   - High-impact, low-effort items first
   - Dependency ordering
   - Risk assessment

2. **Dependency graph:**
   - What must be fixed first
   - What unblocks other fixes
   - Sequencing constraints

3. **Implementation roadmap:**
   - Sequence of changes
   - Estimated timeline
   - Resource requirements

**Output:**
```json
{
  "prioritization": {
    "critical": [
      {
        "issue": "SQL injection vulnerability in user query",
        "fix": "Parameterize all SQL queries",
        "severity": "critical",
        "effort": "15 minutes",
        "impact_score": 9.5,
        "effort_score": 1,
        "roi": 9.5
      }
    ],
    "high": [
      {
        "issue": "N+1 query problem in user API",
        "fix": "Batch load posts per user ID",
        "severity": "high",
        "effort": "30 minutes",
        "impact_score": 8,
        "effort_score": 3,
        "roi": 2.67
      }
    ],
    "medium": [...],
    "low": [...]
  },
  "implementation_order": [
    {
      "rank": 1,
      "fix": "Fix SQL injection vulnerability",
      "blocking": [],
      "unblocks": ["N+1 query optimization"],
      "estimated_time": "15 minutes"
    },
    {
      "rank": 2,
      "fix": "N+1 query optimization",
      "blocking": [],
      "unblocks": [],
      "estimated_time": "30 minutes"
    }
  ],
  "estimated_timeline": "Estimated total: 2 hours for high-priority items, 1 week for all recommendations"
}
```

---

## Output Format: Optimization Report

Optimization mode generates an **Optimization Report** in Markdown:

```markdown
# Optimization Report

## Executive Summary
[Overview of optimization opportunities and impact]

## Performance Bottlenecks
[Ranked by severity and impact]

## Security Vulnerabilities
[Security issues and fixes]

## Efficiency Improvements
[Code quality and maintainability improvements]

## Prioritized Recommendations
[All improvements ranked by impact/effort ratio]

## Implementation Guide
[How to implement each improvement]

## Expected Improvements
[Performance metrics after optimization]
```

---

## Output Formats by Depth

| Depth | Markdown | JSON | Interactive |
|-------|----------|------|-------------|
| **QUICK** | ✅ Optimization report (critical only) | ❌ | ❌ |
| **STANDARD** | ✅ Optimization report (full) | ✅ Structured data | ❌ |
| **DEEP** | ✅ Optimization report (exhaustive) | ✅ Structured data | ✅ Step-through CLI |

---

## Depth Mode Behavior

### QUICK Mode Optimization
**Duration:** 5 minutes | **Tokens:** ~30K | **Accuracy:** 85%

**What's included:**
- High-severity bottlenecks only
- Critical security issues
- Quick wins (low effort)
- Top 3-5 recommendations

**What's skipped:**
- Medium/low severity items
- Detailed analysis
- Implementation code
- Performance metrics

---

### STANDARD Mode Optimization
**Duration:** 15 minutes | **Tokens:** ~60K | **Accuracy:** 95%

**What's included:**
- All bottlenecks (high + medium severity)
- All security issues
- Efficiency improvements
- Implementation code examples
- Effort and impact estimates
- Prioritization by impact/effort ratio
- Performance before/after estimates

**Output formats:** Markdown (optimization report), JSON (structured data)

---

### DEEP Mode Optimization
**Duration:** 30 minutes | **Tokens:** ~90K | **Accuracy:** 99%

**What's included:**
- Everything in STANDARD, plus:
- Low-severity items
- Detailed implementation guides per improvement
- Performance profiling data
- Before/after code examples (10-15 lines)
- Testing strategy for each improvement
- Rollout plan
- Monitoring recommendations
- Long-term optimization roadmap

**Output formats:** Markdown (detailed report), JSON (comprehensive data), Interactive (visual improvement explorer)

---

## Prioritization Framework

Improvements are ranked by Impact/Effort ratio:

### High Impact, Low Effort (Do First)
```
Rank: 1
Impact: High (performance/security improvement)
Effort: Low (< 30 minutes)
ROI: Excellent

Examples:
- Upgrade vulnerable dependency
- Add missing input validation
- Fix N+1 query
```

### High Impact, Medium Effort (Do Next)
```
Rank: 2
Impact: High
Effort: Medium (30 min - 2 hours)
ROI: Good

Examples:
- Refactor complex function
- Add missing indexes
- Implement caching
```

### Medium Impact, Low Effort (Quick Wins)
```
Rank: 3
Impact: Medium
Effort: Low
ROI: Good for effort

Examples:
- Remove dead code
- Simplify conditionals
- Add missing tests
```

### Medium Impact, Medium Effort (Schedule Later)
```
Rank: 4
Impact: Medium
Effort: Medium
ROI: Acceptable

Examples:
- Extract common components
- Refactor middleware
- Add monitoring
```

### High Impact, High Effort (Strategic)
```
Rank: 5
Impact: High
Effort: High (> 4 hours)
ROI: Good but requires planning

Examples:
- Major architectural refactoring
- Database migration
- Move to microservices
```

### Low Impact (Defer or Skip)
```
Rank: 6
Impact: Low
ROI: Low

Examples:
- Style code consistency
- Minor variable renames
- Documentation improvements
```

---

## Examples of Optimization Use Cases

### Example 1: Performance Optimization

**User request:** `"This endpoint is slow, optimize it"`

**Command:** `/code-surgeon --mode=optimization --depth=STANDARD`

**Output highlights:**
- Database query analysis (N+1 detection, missing indexes)
- Response size analysis
- Caching opportunities
- Frontend rendering issues
- Implementation code examples

**Recommendations:**
- Add database index (5 min, 10x faster)
- Batch load related data (30 min, 5x faster)
- Add response caching (20 min, 3x faster)

---

### Example 2: Security Audit

**User request:** `"Find security vulnerabilities"`

**Command:** `/code-surgeon --mode=optimization --depth=DEEP`

**Output highlights:**
- SQL injection risks (with specific line numbers)
- XSS vulnerabilities
- Authentication gaps
- Dependency vulnerabilities
- Remediation code for each

**Critical findings:**
- 2 SQL injection vulnerabilities (fix immediately)
- 1 outdated package with CVE (upgrade)
- Missing CORS validation (add)

---

### Example 3: Bundle Size Reduction

**User request:** `"Reduce bundle size"`

**Command:** `/code-surgeon --mode=optimization --depth=STANDARD`

**Output highlights:**
- Large dependencies
- Code splitting opportunities
- Tree shaking analysis
- Dead code
- Asset optimization

**Improvements found:**
- Remove unused polyfills (50KB)
- Code split vendor code (implement dynamic imports)
- Enable gzip compression (60% size reduction)

---

### Example 4: Technical Debt Cleanup

**User request:** `"What can we improve in this codebase?"`

**Command:** `/code-surgeon --mode=optimization --depth=DEEP`

**Output highlights:**
- Code complexity hotspots
- Duplicated code
- Dead code
- Missing tests
- Inconsistent patterns
- Documentation gaps

**Opportunities:**
- 3 complex functions to refactor
- 5 areas with duplication
- 12 functions lacking tests
- 20% of code is dead

---

## Safety Verification

Optimization mode includes verification:

- ✅ Changes are non-breaking
- ✅ Performance improvements are measurable
- ✅ Security fixes address real vulnerabilities
- ✅ Code quality improvements don't introduce risk
- ✅ Effort estimates are realistic

---

## Common Optimization Scenarios

### Scenario: "Periodic optimization pass"
```bash
# Get full picture of improvement opportunities
/code-surgeon --mode=optimization --depth=STANDARD

# Review all recommendations
# Pick high-impact/low-effort items
# Schedule rest for future sprints
```

### Scenario: "Performance is degrading"
```bash
# Identify what's slow
/code-surgeon --mode=optimization --depth=DEEP

# Focus on bottleneck analysis
# Implement top recommendations
# Measure and verify improvements
```

### Scenario: "Security audit required"
```bash
# Find all vulnerabilities
/code-surgeon --mode=optimization --depth=DEEP

# Review all security issues
# Prioritize by severity
# Create security remediation plan
```

### Scenario: "Reduce technical debt"
```bash
# Get full technical debt assessment
/code-surgeon --mode=optimization --depth=DEEP

# Review efficiency improvements
# Plan refactoring schedule
# Track improvements over time
```

---

## Measuring Improvement

After implementing recommendations:

1. **Performance metrics:**
   - Page load time
   - API response time
   - Database query time
   - Bundle size

2. **Quality metrics:**
   - Code coverage
   - Cyclomatic complexity
   - Test count
   - Documentation coverage

3. **Security metrics:**
   - Known vulnerabilities
   - Dependency age
   - Code review coverage
   - Security test coverage

---

## Next Steps After Optimization Analysis

1. **Review the optimization report**
   - Read all recommendations
   - Understand impact and effort
   - Identify quick wins

2. **Plan implementation**
   - Pick high-impact/low-effort items first
   - Schedule others for future sprints
   - Assign to team members

3. **Implement and verify**
   - For each improvement:
     - Implement the change
     - Run tests
     - Verify metrics improved
     - Merge and deploy

4. **Track improvements**
   - Measure before/after metrics
   - Document learnings
   - Share wins with team
