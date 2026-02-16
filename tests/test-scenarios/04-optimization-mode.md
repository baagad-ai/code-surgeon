# Optimization Mode Test Scenarios

**Mode:** Optimization Mode (O)
**File:** 04-optimization-mode.md
**Total Scenarios:** 24 (8 per depth level)
**Purpose:** Test performance bottleneck detection, security vulnerability scanning, and efficiency analysis across QUICK, STANDARD, and DEEP depths

---

## QUICK Depth (5 minutes, ~30K tokens)

### O-1.1: Performance - React Component Rendering Bottlenecks

**Scenario ID:** O-1.1
**Depth Level:** QUICK
**Type:** Performance Analysis / Rendering Optimization
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A React SPA is experiencing slowdowns, especially when rendering large lists. Developers want quick identification of the top rendering bottlenecks. ~150 files, standard structure.

**Requirement:**
"Analyze React application for rendering performance bottlenecks and optimization opportunities"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "QUICK",
  "repo_root": "/test-repos/react-spa",
  "focus": "performance"
}
```

**Expected Output Structure:**
- Executive Summary (1-2 sentences identifying top bottleneck)
- Top 3-5 Performance Issues
  1. Issue: Missing React.memo() on large list items
     - Impact: Re-renders on parent updates
     - Fix: Wrap component in React.memo()
  2. Issue: Inline style/function creation in render
     - Impact: New reference each render
     - Fix: Move to component level or useMemo()
  3. Issue: Large list without virtualization
     - Impact: DOM overhead, slow initial render
     - Fix: Use react-window or react-virtualized
- Quick Wins (2-3 easy optimizations)
  - Memoization of components
  - useCallback for event handlers
  - useMemo for expensive computations
- Estimated Performance Improvement: 30-50%

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Top 3-5 bottlenecks identified
- [ ] Each issue has clear fix recommendation
- [ ] Output is actionable for developer
- [ ] No false positives (all issues are real)
- [ ] Performance impact estimated
- [ ] Quick wins highlighted

---

### O-1.2: Security - Node.js API Top Vulnerabilities

**Scenario ID:** O-1.2
**Depth Level:** QUICK
**Type:** Security Scanning / Vulnerability Identification
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A Node.js Express API wants quick security assessment. Looking for top vulnerabilities to prioritize fixes. ~120 files, REST API.

**Requirement:**
"Identify top security vulnerabilities in Node.js API"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "QUICK",
  "repo_root": "/test-repos/express-api",
  "focus": "security"
}
```

**Expected Output Structure:**
- Executive Summary (overall security posture)
- Top 3-5 Security Risks
  1. Risk: No input validation on API endpoints
     - Severity: High
     - Fix: Add validation middleware
  2. Risk: SQL injection in raw queries
     - Severity: Critical
     - Fix: Use parameterized queries
  3. Risk: Missing rate limiting
     - Severity: Medium
     - Fix: Add rate limiter middleware
- Quick Fixes (apply immediately)
- Estimated Security Score: Pre-fix vs post-fix

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Top 3-5 vulnerabilities identified
- [ ] Severity levels assigned (Critical, High, Medium)
- [ ] Each has clear fix recommendation
- [ ] Quick wins highlighted
- [ ] No false positives

---

## STANDARD Depth (15 minutes, ~60K tokens)

### O-2.1: Performance - Django Application Bottleneck Analysis

**Scenario ID:** O-2.1
**Depth Level:** STANDARD
**Type:** Performance Analysis / Database & View Optimization
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Django SaaS application is slowing down as data grows. ~250 files, 5+ years old, complex data models. Need comprehensive performance optimization roadmap.

**Requirement:**
"Identify performance bottlenecks in Django application and provide optimization roadmap"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "STANDARD",
  "repo_root": "/test-repos/django-app",
  "focus": "performance"
}
```

**Expected Output Structure:**
- Executive Summary (current bottlenecks, overall score)
- Database Query Analysis
  - N+1 query problems identified (locations)
  - Missing indexes (which fields)
  - Inefficient queries (specific examples)
  - Connection pooling gaps
- View/Response Time Analysis
  - Slow views identified
  - Serialization bottlenecks
  - Template rendering issues
- Caching Opportunities
  - Query caching (Redis setup)
  - Page caching (which pages)
  - Fragment caching
  - Cache invalidation strategy
- Asset & Frontend Optimization
  - Static file serving
  - Minification status
  - Bundling optimization
  - CDN opportunities
- Infrastructure Optimization
  - Database connection pooling
  - Horizontal scaling opportunities
  - Worker process tuning
  - Memory usage patterns
- Prioritized Optimization Roadmap (by impact)
  1. Quick wins (implement first, high impact)
  2. Medium effort (1-2 days each)
  3. Major changes (1-2 weeks)
  4. Strategic improvements (longer term)
- Estimated Performance Improvement: 40-60% overall

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Database issues comprehensively analyzed
- [ ] N+1 queries identified with locations
- [ ] Missing indexes recommended with justification
- [ ] Caching strategy provided
- [ ] 5-8 optimization opportunities listed
- [ ] Roadmap is prioritized by impact
- [ ] Effort estimates provided
- [ ] No false positives

---

### O-2.2: Security - React + Node Full Stack Vulnerability Assessment

**Scenario ID:** O-2.2
**Depth Level:** STANDARD
**Type:** Security Assessment / OWASP Top 10
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A full-stack SaaS (React + Node.js) needs comprehensive security assessment. ~200 files. Want prioritized vulnerability list and fix recommendations.

**Requirement:**
"Perform security assessment against OWASP Top 10 and provide remediation plan"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "STANDARD",
  "repo_root": "/test-repos/react-node-saas",
  "focus": "security"
}
```

**Expected Output Structure:**
- Executive Summary (security score, top risks)
- OWASP Top 10 Assessment
  1. Broken Access Control
     - Found: Missing authorization checks on API endpoints
     - Fix: Add role-based middleware
  2. Cryptographic Failures
     - Found: Passwords not hashed, data not encrypted
     - Fix: Use bcrypt, enable database encryption
  3. Injection
     - Found: SQL injection in raw queries, NoSQL injection
     - Fix: Use parameterized queries, input validation
  4. Insecure Design
     - Found: No rate limiting, weak password policy
     - Fix: Add rate limiting, enforce strong passwords
  5. Security Misconfiguration
     - Found: Debug mode enabled in production, exposed endpoints
     - Fix: Disable debug, secure endpoints
  6. Vulnerable & Outdated Components
     - Found: Outdated dependencies with known CVEs
     - Fix: Update dependencies, audit regularly
  7. Authentication Failures
     - Found: No MFA, session timeout issues
     - Fix: Implement MFA, fix session management
  8. Software & Data Integrity Failures
     - Found: No code signing, no update verification
     - Fix: Implement code signing, verify updates
  9. Logging & Monitoring Failures
     - Found: No security event logging
     - Fix: Log security events, monitor for anomalies
  10. SSRF
      - Found: Server can make arbitrary HTTP requests
      - Fix: Whitelist allowed domains, use firewall rules
- Dependency Vulnerability Analysis
  - Known CVEs in dependencies
  - Severity breakdown (Critical, High, Medium, Low)
  - Outdated packages
- Configuration Review
  - Secrets management (API keys, DB passwords)
  - TLS/HTTPS setup
  - CORS configuration
  - Headers (CSP, X-Frame-Options, etc.)
  - Database configuration
- Authentication & Authorization
  - Password hashing algorithm
  - Session management
  - JWT token validation
  - MFA implementation
  - Permission system
- Data Protection
  - Encryption at rest
  - Encryption in transit
  - Data retention policies
  - PII handling
- Prioritized Remediation Roadmap
  - Critical (fix immediately)
  - High (fix before next release)
  - Medium (schedule for next sprint)
  - Low (monitor/future)
- Timeline & Effort Estimates

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] All 10 OWASP items addressed
- [ ] Specific vulnerabilities found (not generic)
- [ ] Severity levels assigned
- [ ] Fix recommendations clear
- [ ] Dependency vulnerabilities listed with CVE IDs
- [ ] 5-8 security issues identified
- [ ] Remediation roadmap prioritized
- [ ] Timeline realistic

---

### O-2.3: Efficiency - Rails Application Code Quality Analysis

**Scenario ID:** O-2.3
**Depth Level:** STANDARD
**Type:** Efficiency Analysis / Code Quality
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Rails application has accumulated code quality debt. ~400 files, 5+ years old. Team wants to identify efficiency improvements (code duplication, inefficient patterns, maintainability issues).

**Requirement:**
"Analyze Rails application for code quality issues and efficiency improvements"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "STANDARD",
  "repo_root": "/test-repos/rails-app",
  "focus": "efficiency"
}
```

**Expected Output Structure:**
- Executive Summary (code quality score, main areas for improvement)
- Code Duplication Analysis
  - Duplicated logic locations (specific files)
  - Candidates for extraction (services, concerns)
  - Estimated lines to refactor
- Rails Convention Violations
  - Fat models (break into concerns)
  - Fat controllers (move to services)
  - Complex views (extract to partials, helpers)
  - Overly complex routes
- Pattern Inefficiencies
  - Callback abuse (move to services)
  - N+1 queries (despite being in Performance section)
  - Unnecessary scopes
  - Over-reliance on metaprogramming (harder to test)
- Testing Gaps
  - Untested modules
  - Test coverage gaps
  - Fragile tests
  - Missing edge cases
- Dependency Management
  - Unnecessary gems
  - Gem version management
  - Dependency weight
- Maintainability Issues
  - Complex classes/modules (break into smaller pieces)
  - Unclear naming conventions
  - Missing documentation
  - Outdated comments
- Opportunity Areas (prioritized by impact)
  1. Extract 3-4 services (high impact, medium effort)
  2. Refactor fat models (high impact, medium effort)
  3. Increase test coverage (medium impact, medium effort)
  4. Remove unused code (low impact, low effort)
  5. Update documentation (low impact, low effort)
- Estimated Effort: 2-4 weeks of refactoring

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Code duplication quantified with locations
- [ ] Rails convention violations found
- [ ] Pattern inefficiencies identified
- [ ] Testing gaps documented
- [ ] Gem dependencies reviewed
- [ ] 5-8 improvement opportunities listed
- [ ] Opportunity list prioritized by impact
- [ ] Effort estimates realistic

---

### O-2.4: Tech Debt - Legacy Node Application Modernization Roadmap

**Scenario ID:** O-2.4
**Depth Level:** STANDARD
**Type:** Tech Debt Assessment / Modernization Strategy
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Node.js application built 8 years ago is working but difficult to maintain. Callback hell, no tests, many outdated patterns. Team wants prioritized modernization roadmap.

**Requirement:**
"Assess tech debt in legacy Node.js application and create modernization roadmap"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "STANDARD",
  "repo_root": "/test-repos/legacy-node-app",
  "focus": "tech-debt"
}
```

**Expected Output Structure:**
- Executive Summary (debt score, modernization timeline estimate)
- Tech Debt Categories
  - Code Quality (callback hell, no typing, etc.)
  - Testing (lack of tests, fragile tests)
  - Dependencies (outdated, security vulnerabilities)
  - Patterns (outdated Node.js patterns)
  - Infrastructure (old deployment, no containerization)
  - Documentation (missing or outdated)
- Debt Severity Assessment
  - Blocking new features
  - Causing bugs
  - Making onboarding hard
  - Creating security risks
  - Performance issues
- Modernization Opportunities (prioritized)
  1. Async/await migration (high impact, medium effort)
  2. Add test coverage (medium impact, medium effort)
  3. Update dependencies (high impact, low-medium effort)
  4. Typing (TypeScript migration) (medium impact, high effort)
  5. Code organization (medium impact, medium effort)
  6. Infrastructure (low impact, medium effort)
- Phased Roadmap
  - Phase 1 (Month 1): Quick wins + dependency updates
  - Phase 2 (Months 2-3): Async/await migration
  - Phase 3 (Months 4-6): Testing infrastructure
  - Phase 4 (Months 6-9): TypeScript (optional)
  - Phase 5 (Months 9-12): Full modernization
- Resource Requirements (team, external help)
- Expected Outcome (what application looks like post-modernization)

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Tech debt comprehensively categorized
- [ ] Severity assessment realistic
- [ ] Blocking issues identified
- [ ] Modernization opportunities listed
- [ ] Roadmap is phased (can be done incrementally)
- [ ] Timeline realistic (8-12 months typical)
- [ ] Resource requirements clear
- [ ] Quick wins highlighted (can start immediately)

---

## DEEP Depth (30 minutes, ~90K tokens)

### O-3.1: Comprehensive Optimization - High-Traffic SaaS

**Scenario ID:** O-3.1
**Depth Level:** DEEP
**Type:** Comprehensive Optimization / All Three Areas
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A high-traffic SaaS platform (React + Node.js + PostgreSQL) serves 100K+ users, experiencing performance degradation and security concerns. ~600 files. Need comprehensive analysis across performance, security, and efficiency.

**Requirement:**
"Comprehensive optimization analysis: performance bottlenecks, security vulnerabilities, and efficiency improvements"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "DEEP",
  "repo_root": "/test-repos/saas-hightraffic",
  "focus": "comprehensive"
}
```

**Expected Output Structure:**
- Executive Summary (3-4 sentences with scores)
  - Performance score: 6/10 (below expectations for scale)
  - Security score: 7/10 (adequate but gaps)
  - Efficiency score: 5/10 (significant tech debt)
  - Recommendation: Prioritize performance, then security
- Part 1: Performance Analysis (Detailed)
  - Current Performance Metrics
    - Page load time (target <2s, actual 4-5s)
    - API response time (target <200ms, actual 300-800ms)
    - Database query time distribution
    - Concurrent user capacity
  - Performance Bottleneck Identification
    - Database bottlenecks
      - Slow queries (identify top 10 queries by cumulative time)
      - Missing indexes (which tables)
      - N+1 query patterns (where they occur)
      - Connection pool exhaustion (frequency)
    - API bottlenecks
      - Serialization time (large responses)
      - Middleware overhead
      - Third-party API calls (blocking?)
      - Cache misses
    - Frontend bottlenecks
      - Large bundle size
      - Rendering performance (FCP, LCP, CLS metrics)
      - Network waterfall analysis
      - Component re-render frequency
  - Caching Strategy Analysis
    - Current caching (Redis, in-memory, HTTP)
    - Cache hit rate (what % of requests hit cache)
    - Cache invalidation strategy (is it correct?)
    - Caching opportunities (what else should be cached)
  - Database Optimization
    - Index analysis (missing, inefficient, unused)
    - Query optimization opportunities
    - Connection pooling configuration
    - Replication/sharding consideration
    - Query result caching
    - Database version/configuration review
  - Infrastructure Optimization
    - Load balancing (is it even?)
    - Horizontal scaling (can we add more nodes?)
    - CDN setup (static assets)
    - Monitoring/observability
    - Auto-scaling configuration
  - Optimization Roadmap (prioritized by impact)
    1. Add missing database indexes (high impact, low effort, immediate)
    2. Implement Redis caching for hot queries (high impact, low-medium effort, 1-2 days)
    3. Optimize slow API endpoints (medium impact, medium effort, 2-4 days)
    4. Frontend bundle optimization (medium impact, medium effort, 1-2 days)
    5. Add CDN for static assets (medium impact, low effort, 1 day)
    6. Query result pagination (high impact, medium effort, 2-3 days)
    7. Background job processing (high impact, medium-high effort, 3-5 days)
    8. Database read replicas (medium impact, high effort, 1-2 weeks)
    9. Horizontal scaling infrastructure (medium impact, high effort, 2-3 weeks)
  - Expected Performance Improvement: 60-70% overall latency reduction
- Part 2: Security Analysis (Detailed)
  - Current Security Posture
    - Security score (0-100)
    - Compliance status (GDPR, SOC 2, PCI if applicable)
    - Known vulnerabilities (CVE count)
    - Recent security incidents (if any)
  - OWASP Top 10 Assessment
    - Broken Access Control
      - Found: API endpoints lack authorization checks
      - Risk: Users can access others' data
      - Fix: Add authorization middleware, audit endpoints
    - Cryptographic Failures
      - Found: Passwords using old hashing, data not encrypted
      - Risk: Password breach, data exposure
      - Fix: Upgrade to argon2, enable database encryption
    - Injection
      - Found: SQL injection in 2-3 locations, unsanitized HTML
      - Risk: Code execution, XSS attacks
      - Fix: Use parameterized queries, sanitize output
    - Insecure Design
      - Found: No rate limiting, weak password policy
      - Risk: Brute force attacks, DDoS
      - Fix: Add rate limiting, enforce strong passwords
    - Security Misconfiguration
      - Found: Debug mode enabled, exposed endpoints, CORS too permissive
      - Risk: Information disclosure, unauthorized access
      - Fix: Disable debug mode, secure endpoints, tighten CORS
    - Vulnerable & Outdated Components
      - Found: 15+ high-severity CVEs in dependencies
      - Risk: Known exploits available
      - Fix: Update dependencies immediately
    - Authentication Failures
      - Found: No MFA, session timeout 30 days
      - Risk: Account compromise
      - Fix: Implement MFA, reduce session timeout
    - Software & Data Integrity Failures
      - Found: No code signing, dependencies not verified
      - Risk: Supply chain attacks
      - Fix: Implement code signing, verify dependencies
    - Logging & Monitoring Failures
      - Found: No security event logging
      - Risk: Can't detect attacks
      - Fix: Log security events, set up alerting
    - SSRF
      - Found: Server can make requests to internal IPs
      - Risk: Port scanning, information disclosure
      - Fix: Whitelist allowed domains, add firewall rules
  - Dependency Vulnerability Analysis
    - High-severity CVEs (with details)
    - Medium-severity vulnerabilities
    - Outdated packages (end-of-life)
    - Packages with slow update cycles
  - Configuration Security Review
    - Secrets management (are keys in code?)
    - Environment variables (are they secure?)
    - TLS/HTTPS (is it enforced?)
    - CORS configuration (too permissive?)
    - Security headers (CSP, X-Frame-Options, etc.)
    - Database configuration (is it secure?)
  - Data Protection Assessment
    - Data at rest encryption
    - Data in transit encryption (TLS)
    - Data access controls (who can see what)
    - Data retention policies
    - PII handling
    - Backup security
  - Infrastructure Security
    - Network segmentation
    - Firewall rules
    - Container security
    - Kubernetes security (if applicable)
  - Security Remediation Roadmap (prioritized by risk)
    1. Update dependencies with CVEs (critical, immediate, 1 day)
    2. Add MFA (high, this week, 2-3 days)
    3. Fix authorization bugs (high, this week, 3-5 days)
    4. Add rate limiting (high, 1-2 days)
    5. Implement security logging (high, 2-3 days)
    6. Upgrade password hashing (medium, 2-3 days)
    7. Add HSTS/CSP headers (medium, 1 day)
    8. Database encryption (medium, 3-5 days)
    9. Code signing (low, 2-3 days)
  - Security Testing Recommendations
    - Penetration testing (hire professionals)
    - Dependency security audits (automated + manual)
    - Code security scanning (SonarQube, Snyk)
    - Regular security training for team
- Part 3: Efficiency Analysis (Detailed)
  - Code Quality Assessment
    - Code duplication (% of codebase that's duplicated)
    - Cyclomatic complexity (overly complex functions)
    - Module coupling (tightly coupled modules)
    - Code documentation (% of code documented)
    - Naming consistency (are conventions followed?)
  - Technical Debt Quantification
    - Debt score (0-100, lower is better)
    - Top 10 areas of debt
    - Estimated remediation effort
    - Interest cost (how much is it slowing down development?)
  - Code Duplication Analysis
    - Locations of duplication
    - Candidates for extraction
    - Estimated lines to consolidate
  - Architecture Review
    - Module boundaries (clear or blurry?)
    - Dependency flow (circular dependencies?)
    - Scalability (can it scale?)
    - Testability (easy to test?)
  - Testing Coverage
    - Unit test coverage (%)
    - Integration test coverage (%)
    - E2E test coverage (%)
    - Gap analysis (what's not tested?)
  - Dependency Management
    - Dependency count (too many?)
    - Unused dependencies
    - Dependency conflicts
    - License compliance issues
  - Documentation Review
    - Architecture documentation (exists?)
    - API documentation (complete?)
    - Deployment documentation (clear?)
    - Runbook documentation (for operations)
  - Efficiency Improvement Roadmap (prioritized)
    1. Add test coverage (medium impact, high effort, 2-4 weeks)
    2. Extract duplicate code (medium impact, medium effort, 1-2 weeks)
    3. Refactor complex modules (medium impact, medium effort, 1-2 weeks)
    4. Update documentation (low impact, low effort, 3-5 days)
    5. Remove unused dependencies (low impact, low effort, 1 day)
    6. TypeScript migration (high impact, very high effort, 4-6 weeks)
    7. Module reorganization (high impact, medium effort, 1-2 weeks)
- Consolidated Optimization Timeline
  - Week 1: Security critical fixes + performance quick wins
  - Weeks 2-4: Performance medium-term fixes + security hardening
  - Weeks 5-8: Efficiency improvements + architectural refactoring
  - Weeks 9+: Major initiatives (TypeScript, infrastructure scaling)
- Estimated Business Impact
  - Performance improvement: 40-50% latency reduction = 30% more throughput capacity
  - Security improvement: 90% risk reduction = reduced compliance audit costs
  - Efficiency: 20-30% faster development velocity
  - ROI: 3-4x return on investment in 6 months
- Resource Requirements
  - Team composition (backend engineer, frontend engineer, DevOps)
  - Duration (4-6 months with current team size)
  - External help (security audit, performance testing)

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] All three areas (performance, security, efficiency) comprehensively analyzed
- [ ] Specific bottlenecks identified with locations
- [ ] OWASP Top 10 items all addressed
- [ ] CVEs and vulnerabilities listed with severity
- [ ] Code quality issues quantified
- [ ] Tech debt assessed
- [ ] 20+ optimization opportunities across all areas
- [ ] Timeline realistic (4-6 months for comprehensive work)
- [ ] Roadmap prioritized by impact
- [ ] Business impact estimated
- [ ] Resource requirements clear

---

### O-3.2: Comprehensive Optimization - Microservices Platform

**Scenario ID:** O-3.2
**Depth Level:** DEEP
**Type:** Comprehensive Optimization / Distributed Systems
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A microservices platform (10+ services, Kubernetes, gRPC + HTTP) is experiencing performance degradation and operational complexity. ~1000+ files across services. Cross-service optimization opportunities.

**Requirement:**
"Optimize microservices platform: inter-service performance, distributed security, and operational efficiency"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "DEEP",
  "repo_root": "/test-repos/microservices-platform",
  "focus": "comprehensive"
}
```

**Expected Output Structure:**
- Executive Summary
  - Platform health: performance, security, stability
  - Top 3 optimization areas (cross-service latency, security gaps, operational complexity)
  - Timeline estimate: 8-12 weeks
- Part 1: Inter-Service Performance Optimization
  - Service Call Analysis
    - Latency per service pair (which calls are slow?)
    - Call frequency (which calls are most common?)
    - Error rates per service
    - Timeout issues (where are timeouts happening?)
  - Communication Protocol Analysis
    - gRPC usage (is it used effectively?)
    - HTTP/REST usage (where could gRPC be used?)
    - Message size analysis (large payloads?)
    - Serialization overhead
  - Service Mesh Analysis (if using Istio, Linkerd, etc.)
    - Istio sidecar overhead
    - Network policies effectiveness
    - Load balancing strategy
    - Circuit breaker configuration
  - Caching Strategy for Distributed Systems
    - Cache-aside pattern opportunities
    - Write-through caching
    - Event-driven cache invalidation
    - Distributed cache coherence
  - Optimization Opportunities
    - Replace HTTP with gRPC (where applicable)
    - Implement caching for hot data
    - Batch requests (reduce call frequency)
    - Connection pooling
    - Circuit breakers (prevent cascading failures)
  - Timeline & Effort: 2-3 weeks
- Part 2: Distributed Security Hardening
  - Service-to-Service Authentication
    - Current mechanism (mTLS? API keys? OAuth?)
    - Gaps found
    - Recommendations (mTLS recommended)
  - Network Policies
    - Are services only talking to necessary services?
    - Overly permissive policies
    - Recommendations (zero-trust model)
  - Secret Management
    - How are API keys, DB passwords stored?
    - Rotation mechanism
    - Access control (who can access secrets?)
  - Compliance Across Services
    - Data isolation between services
    - PII handling per service
    - Audit trail across services
  - Vulnerability Management
    - Known CVEs per service
    - Dependency alignment (same lib versions across services?)
  - Remediation Roadmap
    1. Implement mTLS (2-3 weeks)
    2. Tighten network policies (1 week)
    3. Secret management centralization (2-3 weeks)
    4. Vulnerability scanning (1 week + ongoing)
  - Timeline & Effort: 3-4 weeks
- Part 3: Operational Efficiency Improvements
  - Observability & Monitoring
    - Distributed tracing (is it complete?)
    - Metrics collection (coverage?)
    - Centralized logging (yes? no?)
    - Alert rules (appropriate thresholds?)
  - Deployment & CI/CD
    - Build time per service
    - Deployment frequency
    - Rollback mechanism
    - Blue/green or canary deployments?
  - Infrastructure Management
    - Kubernetes configuration (resource requests/limits set?)
    - Autoscaling (HPA configured correctly?)
    - Pod disruption budgets (set?)
    - Storage provisioning
  - Data Management
    - Database scaling strategy
    - Data consistency across services
    - Backup/recovery per service
  - Developer Experience
    - Local development setup (easy?)
    - Testing (unit, integration, E2E)
    - Documentation (up to date?)
    - Debugging tools (available?)
  - Optimization Opportunities
    - Add distributed tracing (if missing)
    - Centralize logging
    - Improve CI/CD build times (parallel builds)
    - Better Kubernetes resource management
    - Automated testing improvements
  - Timeline & Effort: 2-3 weeks
- Consolidated Roadmap (8-12 weeks total)
  - Phase 1 (Weeks 1-2): Performance quick wins + security urgencies
  - Phase 2 (Weeks 3-4): Service communication optimization
  - Phase 3 (Weeks 5-7): mTLS and network policies
  - Phase 4 (Weeks 8-9): Observability improvements
  - Phase 5 (Weeks 10-12): Operational automation
- Expected Outcomes
  - 40% reduction in inter-service latency
  - 95% security compliance (zero-trust model)
  - 50% reduction in operational overhead
  - Team velocity increase (easier to operate)

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] All services identified and analyzed
- [ ] Inter-service call patterns documented
- [ ] Latency issues specific (not generic)
- [ ] Security gaps in distributed context addressed
- [ ] Operational complexity challenges identified
- [ ] Roadmap is phased and realistic
- [ ] Timeline 8-12 weeks (reasonable for complex systems)
- [ ] Cross-service optimization opportunities clear

---

### O-3.3: Comprehensive Optimization - Data-Intensive Application

**Scenario ID:** O-3.3
**Depth Level:** DEEP
**Type:** Comprehensive Optimization / Data & Analytics Focus
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A data-intensive application (ETL, analytics, reporting) processes TB+ of data daily. ~400 files, Python backend, complex queries. Performance is critical for business reports. SLA: reports finish within 2 hours.

**Requirement:**
"Optimize data-intensive application: query performance, data pipeline efficiency, and analytics bottlenecks"

**Input Contract:**
```json
{
  "mode": "optimization",
  "depth": "DEEP",
  "repo_root": "/test-repos/data-analytics-app",
  "focus": "comprehensive"
}
```

**Expected Output Structure:**
- Executive Summary
  - Current performance: reports finishing in 4-6 hours (2x SLA)
  - Top bottlenecks: slow ETL, query optimization, missing indexes
  - Optimization potential: 50-60% improvement (get to <2 hour target)
- Part 1: ETL Pipeline Optimization
  - Data Ingestion Analysis
    - Data volume (TB per day)
    - Ingestion rate (records/sec)
    - Bottlenecks (where does it slow down?)
    - Error rates/recovery time
  - Data Transformation Analysis
    - Transformation logic (Python code review)
    - Slow operations (identify top 5 slow transforms)
    - Memory usage during transforms
    - Parallelization opportunities
  - Data Loading
    - Target database (PostgreSQL, Redshift, BigQuery?)
    - Batch vs streaming
    - Loading performance
    - Constraints/validation
  - Optimization Opportunities
    - Parallelize ingestion (if sequential)
    - Batch processing instead of row-by-row
    - Vectorization (use NumPy/Pandas for bulk operations)
    - Incremental loading (only new data)
    - Data partitioning strategy
  - Expected Improvement: 30-40% reduction in ETL time
  - Timeline: 2-3 weeks
- Part 2: Query Performance Optimization
  - Query Analysis
    - Slow queries (identify top 10 by execution time)
    - Query patterns (JOINs, aggregations, etc.)
    - Data volume (how many rows per query?)
    - Index usage (are indexes used?)
  - Indexing Strategy
    - Missing indexes (recommend specific indexes)
    - Index effectiveness (are they being used?)
    - Composite indexes (for multi-column queries)
    - Partitioning (table partitioning for large tables?)
  - Query Optimization
    - Rewrite slow queries
    - Use EXPLAIN ANALYZE to find issues
    - Aggregation optimization
    - JOIN optimization
    - Subquery optimization
  - Materialized Views
    - Pre-aggregate common reports
    - Materialized view refresh strategy
    - Cost/benefit analysis
  - Caching Strategy
    - Cache hot queries (Redis)
    - Cache invalidation
    - Time-to-live (TTL) settings
  - Expected Improvement: 40-50% reduction in query time
  - Timeline: 2-3 weeks
- Part 3: Analytics & Reporting Optimization
  - Report Generation Analysis
    - Report types (dashboards, batch reports, real-time?)
    - Current generation time
    - Data freshness requirements
  - Dashboard Optimization
    - Query count per dashboard
    - Data point count (too many to render?)
    - Frontend performance
    - Caching for dashboards
  - Batch Report Optimization
    - Dependency chain (reports depend on other reports?)
    - Parallelization opportunities
    - Incremental generation (update deltas only)
    - Compression for storage
  - Real-Time Analytics
    - Streaming data processing
    - Windowed aggregations
    - Anomaly detection performance
  - Optimization Opportunities
    - Pre-generate reports (off-peak hours)
    - Incremental generation
    - Dashboard caching (Redis)
    - Data aggregation (pre-aggregate common metrics)
  - Expected Improvement: 30-40% reduction in report time
  - Timeline: 1-2 weeks
- Part 4: Infrastructure & Resource Optimization
  - Database Configuration
    - Memory allocation (shared_buffers, work_mem)
    - Connection pooling
    - Vacuum/autovacuum settings
    - Checkpoints
  - Resource Allocation
    - CPU usage per operation
    - Memory peaks
    - Disk I/O bottlenecks
  - Scaling Strategy
    - Vertical scaling (bigger database machine?)
    - Horizontal scaling (sharding?)
    - Read replicas (for reporting)
  - Cost Optimization
    - Resource utilization efficiency
    - Cloud provider recommendations (if cloud-hosted)
  - Timeline: 1-2 weeks
- Part 5: Caching & Performance Monitoring
  - Caching Architecture
    - What to cache (queries, computations, aggregations)
    - Where to cache (Redis, in-memory, database)
    - Cache invalidation strategy
    - Cache hit rate monitoring
  - Performance Monitoring
    - Query execution time tracking
    - ETL timing per step
    - Report generation time tracking
    - SLA compliance tracking
    - Alerting on slow operations
  - Profiling & Analysis Tools
    - Python profiling (line_profiler, cProfile)
    - SQL query profiling (EXPLAIN ANALYZE)
    - Memory profiling (memory_profiler)
    - Custom metrics
  - Timeline: 1-2 weeks
- Consolidated Optimization Roadmap (6-8 weeks total)
  - Week 1: Index creation + quick query optimizations
  - Weeks 2-3: ETL optimization (parallelization, batching)
  - Weeks 4-5: Query rewrites + materialized views
  - Weeks 6-7: Caching implementation + monitoring
  - Week 8: Testing & fine-tuning
- Expected Outcomes
  - ETL time: 4-6 hours → 3 hours (50% improvement)
  - Query time: 2 hours → 1 hour (50% improvement)
  - Report time: 2 hours → 1 hour (50% improvement)
  - Total: 4-6 hours → <2 hours (60% improvement, meets SLA)
- Cost Analysis
  - Development effort: 4-6 weeks
  - Infrastructure changes (if needed)
  - Operational cost changes (monitoring, caching)
  - Business benefit: SLA met, faster insights

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] ETL pipeline analyzed with specific bottlenecks
- [ ] Slow queries identified (top 10 with execution times)
- [ ] Indexing strategy specific (which columns, which order)
- [ ] Caching strategy appropriate for data-intensive workload
- [ ] Materialized view opportunities identified
- [ ] Reporting optimization covers dashboards + batch reports
- [ ] Infrastructure recommendations realistic
- [ ] SLA impact clear (targets achievable)
- [ ] Timeline realistic (6-8 weeks)
- [ ] Cost-benefit analysis included

---

## Validation Checklist (All Optimization Scenarios)

After running each scenario, verify:

### Output Structure
- [ ] Executive summary with clear metrics
- [ ] Specific issues identified (not generic)
- [ ] Recommendations actionable
- [ ] Effort estimates provided
- [ ] All promised sections present

### Content Quality
- [ ] Issues are real (verify against codebase)
- [ ] Impact assessments realistic
- [ ] Improvements achievable in timeline
- [ ] No false positives
- [ ] Recommendations tested/proven

### Depth Adherence
- [ ] QUICK: 3-5 issues, brief description, quick fix
- [ ] STANDARD: 5-8 issues, detailed with effort, roadmap
- [ ] DEEP: 15-20 issues across all areas, comprehensive

### Metrics
- [ ] Execution time within ±30% of estimate
- [ ] Token usage within ±20% of budget
- [ ] Issue identification accuracy >90%
- [ ] No PII or secrets in output
