# Implementation Planning Mode Test Scenarios

**Mode:** Implementation Planning (IP)
**File:** 01-implementation-planning.md
**Total Scenarios:** 24 (8 per depth level)
**Purpose:** Test feature implementation, bug fixes, and refactoring guidance across QUICK, STANDARD, and DEEP depths

---

## QUICK Depth (5 minutes, ~30K tokens)

### IP-1.1: Simple Feature - Password Reset

**Scenario ID:** IP-1.1
**Depth Level:** QUICK
**Type:** Build - New Feature
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A junior developer needs to add a password reset feature to an existing Express + React application with basic auth already implemented. The codebase has ~100 files, straightforward structure.

**Requirement:**
"Add a password reset feature to the authentication system"

**Input Contract:**
```json
{
  "requirement": "Add a password reset feature to the authentication system",
  "mode": "implementation",
  "depth": "QUICK",
  "repo_root": "/test-repos/express-react-auth",
  "issue_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary (1-2 sentences)
- Tech Stack Overview (3-4 lines)
- 5-Section Surgical Prompts:
  1. Task Breakdown (3-4 tasks)
  2. Phase 1 Prompt
  3. Phase 2 Prompt
  4. Phase 3 Prompt
  5. Validation Checklist

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Identifies password reset flow (forgot → email → reset link → new password)
- [ ] Mentions email service integration (specific service options suggested)
- [ ] Suggests security best practices (token expiration ≤1 hour, rate limiting ≤5/minute)
- [ ] Provides at least 3 concrete tasks with effort estimates
- [ ] Output length: QUICK <500 lines (concise implementation guidance)
- [ ] No Tier 3 patterns included (QUICK skips deep refactoring)
- [ ] Mentions existing auth structure (JWT, sessions, or other)
- [ ] Suggests testing approach (≥3 test scenarios)

---

### IP-1.2: Simple Feature - SSO Integration

**Scenario ID:** IP-1.2
**Depth Level:** QUICK
**Type:** Build - New Feature
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A React SPA with Express backend needs to integrate Google/GitHub OAuth. Existing JWT authentication in place. ~80 files, clear separation of concerns.

**Requirement:**
"Implement single sign-on (SSO) with Google and GitHub"

**Input Contract:**
```json
{
  "requirement": "Implement single sign-on (SSO) with Google and GitHub",
  "mode": "implementation",
  "depth": "QUICK",
  "repo_root": "/test-repos/saas-app",
  "issue_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary
- Tech Stack (1-2 sentences)
- 5-Section Surgical Prompts:
  1. Task Breakdown (3 main tasks: setup, integration, fallback)
  2. OAuth Setup Prompt
  3. Frontend Integration Prompt
  4. Backend Integration Prompt
  5. Validation Checklist

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Identifies OAuth2 flow correctly
- [ ] Mentions token handling (redirect, exchange, storage)
- [ ] Suggests fallback for users without OAuth
- [ ] Identifies provider-specific configuration
- [ ] Suggests testing strategy (test with real credentials or mocks)
- [ ] Mentions security (CSRF protection, state parameter)
- [ ] Output is 50% shorter than STANDARD equivalent
- [ ] Focuses on high-level phases, not detailed code snippets

---

## STANDARD Depth (15 minutes, ~60K tokens)

### IP-2.1: Mid-Complexity Feature - Two-Factor Authentication

**Scenario ID:** IP-2.1
**Depth Level:** STANDARD
**Type:** Build - New Feature
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A SaaS platform (Django backend + React frontend) needs 2FA support. Already has email-based auth. ~200 files, modular structure, has test infrastructure. Users are non-technical founders who want security without complexity.

**Requirement:**
"Add two-factor authentication using TOTP (authenticator apps) and SMS backup codes"

**Input Contract:**
```json
{
  "requirement": "Add two-factor authentication using TOTP and SMS backup codes",
  "mode": "implementation",
  "depth": "STANDARD",
  "repo_root": "/test-repos/django-saas",
  "issue_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary (2 sentences with context)
- Architecture Diagram (ASCII or text description)
- Tech Stack Analysis (5-6 lines: identify ORM, auth library, testing setup)
- Risk Assessment (potential issues: clock skew, SMS delivery, backup code storage)
- 9-Section Surgical Prompts:
  1. Task Breakdown (8-10 detailed tasks)
  2. Database Schema Changes
  3. Backend 2FA Setup (TOTP generation)
  4. Backup Code Generation & Storage
  5. API Endpoints (enable 2FA, verify TOTP, use backup codes)
  6. Frontend: 2FA Setup Flow
  7. Frontend: 2FA Verification
  8. Testing Strategy
  9. Rollout Checklist (feature flags, gradual rollout)
- Dependency Changes (suggest libraries: pyotp, qrcode, etc.)
- Team Guidelines Integration (follow existing patterns)

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Comprehensive task breakdown (8+ tasks)
- [ ] Addresses TOTP setup with QR code generation
- [ ] Plans backup codes (generation, storage, one-time use)
- [ ] SMS backup considered with rate limiting
- [ ] Identifies clock skew risks
- [ ] Suggests database schema changes
- [ ] Provides API endpoint specifications
- [ ] Frontend flow detailed (setup, verification, recovery)
- [ ] Testing approach includes edge cases (expired codes, invalid tokens)
- [ ] Rollout strategy includes feature flags
- [ ] Mentions team conventions found in codebase
- [ ] Output is 3-5x more detailed than QUICK
- [ ] Includes code snippets for complex operations

---

### IP-2.2: Bug Fix - Memory Leak in Event Emitter

**Scenario ID:** IP-2.2
**Depth Level:** STANDARD
**Type:** Fix - Bug Resolution
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Node.js application using EventEmitter has a memory leak. The team suspects missing `.off()` calls in cleanup. ~150 files, moderate complexity, has performance monitoring in place. Critical production issue.

**Requirement:**
"Fix memory leak in event emitter - process memory grows unbounded"

**Input Contract:**
```json
{
  "requirement": "Fix memory leak in event emitter - process memory grows unbounded",
  "mode": "implementation",
  "depth": "STANDARD",
  "repo_root": "/test-repos/node-app-leak",
  "issue_type": "bug",
  "github_issue": "https://github.com/org/repo/issues/423"
}
```

**Expected Output Structure:**
- Executive Summary (identify likely root cause)
- Root Cause Analysis
  - Probable cause: event listener accumulation
  - Affected modules
  - Impact assessment
- Tech Stack Overview
- Investigation Checklist (what to look for)
- 9-Section Surgical Prompts:
  1. Memory Profiling Guide (using Node inspection tools)
  2. Code Search Strategy (find event emitter instantiation)
  3. Listener Registration Audit (where are `.on()` calls?)
  4. Cleanup Analysis (where should `.off()` go?)
  5. Fix Implementation (apply cleanup)
  6. Weak Reference Refactor (if applicable)
  7. Testing Strategy (memory regression tests)
  8. Monitoring Setup (prevent future leaks)
  9. Rollout Verification

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Provides memory profiling instructions
- [ ] Identifies likely patterns causing leak (missing `.off()`, circular refs)
- [ ] Suggests search patterns to find problem code
- [ ] Proposes cleanup strategies
- [ ] Mentions WeakMap/WeakSet if applicable
- [ ] Suggests memory regression tests
- [ ] Monitoring/alerting setup included
- [ ] Provides rollout verification steps
- [ ] Mentions performance impact of fix

---

### IP-2.3: Refactoring - Extract Authentication to Service

**Scenario ID:** IP-2.3
**Depth Level:** STANDARD
**Type:** Refactor - Code Organization
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A monolithic Express app has authentication logic scattered across 5 files (middleware, routes, models). The team wants a dedicated auth service. ~250 files, established patterns, moderate test coverage.

**Requirement:**
"Extract authentication logic into a dedicated authentication service to improve maintainability and testability"

**Input Contract:**
```json
{
  "requirement": "Extract authentication logic into a dedicated authentication service",
  "mode": "implementation",
  "depth": "STANDARD",
  "repo_root": "/test-repos/express-monolith",
  "issue_type": "refactor"
}
```

**Expected Output Structure:**
- Executive Summary (scope of refactoring)
- Architecture Changes (before/after diagram)
- Affected Components (which files will change)
- Risk Assessment (breaking changes, testing requirements)
- Dependency Analysis (what the service will depend on)
- 9-Section Surgical Prompts:
  1. Service Design (interface definition)
  2. Method Extraction (what methods to move)
  3. Dependency Injection Setup
  4. Middleware Refactoring
  5. Route Handler Refactoring
  6. Model Integration (if using ORM)
  7. Testing: Unit Tests
  8. Testing: Integration Tests
  9. Rollout Strategy (phased migration)
- Team Guidelines Alignment (existing patterns)
- Breaking Change Analysis

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Clear before/after architecture
- [ ] All affected files identified
- [ ] Service interface specification provided
- [ ] Dependency injection patterns match codebase
- [ ] Breaking changes identified
- [ ] Testing strategy comprehensive (unit + integration)
- [ ] Rollout is phased (don't break existing code)
- [ ] Team conventions followed
- [ ] Code organization improves (DRY, separation of concerns)

---

### IP-2.4: Major Feature - REST to GraphQL Migration

**Scenario ID:** IP-2.4
**Depth Level:** STANDARD
**Type:** Build - Architecture Migration
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A mature Node.js + React application with 30+ REST endpoints wants to migrate to GraphQL. ~400 files, complex data model, significant user base. Must maintain backward compatibility during transition.

**Requirement:**
"Migrate from REST API to GraphQL API while maintaining backward compatibility"

**Input Contract:**
```json
{
  "requirement": "Migrate from REST API to GraphQL API with backward compatibility",
  "mode": "implementation",
  "depth": "STANDARD",
  "repo_root": "/test-repos/rest-to-graphql",
  "issue_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary (phased approach)
- Architecture Comparison (REST vs GraphQL)
- Tech Stack (suggest Apollo Server, client library)
- Migration Strategy (parallel running both APIs)
- Phase Breakdown (server, client, cleanup)
- 9-Section Surgical Prompts:
  1. GraphQL Schema Design (from REST models)
  2. Apollo Server Setup
  3. Resolver Implementation (1st batch of endpoints)
  4. Authentication Integration (JWT with GraphQL)
  5. Frontend: Apollo Client Setup
  6. Frontend: Refactor Queries (1st batch)
  7. Backward Compatibility (REST proxy layer)
  8. Testing Strategy (query validation, resolver tests)
  9. Rollout Plan (phased endpoint migration, monitoring)
- Risk Analysis (N+1 queries, caching strategy)
- Performance Considerations
- Timeline Estimate

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Phased approach prevents breaking changes
- [ ] Schema design clearly derived from REST models
- [ ] Apollo Server setup with existing auth
- [ ] Backward compatibility layer maintained
- [ ] N+1 query problem addressed
- [ ] Caching strategy included
- [ ] Testing covers queries and resolvers
- [ ] Frontend refactoring guidance provided
- [ ] Monitoring/metrics approach specified
- [ ] Timeline realistic for team

---

## DEEP Depth (30 minutes, ~90K tokens)

### IP-3.1: Complex Architecture - Database Schema Redesign for Multi-Tenancy

**Scenario ID:** IP-3.1
**Depth Level:** DEEP
**Type:** Build - Architectural Redesign
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A SaaS application (Django + PostgreSQL) was built as single-tenant and needs multi-tenancy support. ~600 files, complex relational schema, large existing data set (1M+ records), critical production system. Multiple teams need coordination.

**Requirement:**
"Redesign database schema to support multi-tenancy (multiple isolated customer databases or shared schema with tenant isolation)"

**Input Contract:**
```json
{
  "requirement": "Redesign database schema to support multi-tenancy",
  "mode": "implementation",
  "depth": "DEEP",
  "repo_root": "/test-repos/saas-multitenancy",
  "issue_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary (2-3 sentences)
- Architectural Decision Record (ADR)
  - Problem statement
  - Considered solutions (isolated DBs vs shared schema)
  - Decision with rationale
  - Implications
- Comprehensive Tech Stack Analysis
  - Current: ORM, database, libraries
  - Changes needed: multi-tenancy library options
- Data Migration Strategy (complex, large dataset)
- Risk Assessment (data integrity, compliance, testing)
- Dependency & Module Impact Analysis (affects all major modules)
- 12-15 Section Surgical Prompts:
  1. Multi-Tenancy Model Selection (isolated vs shared schema)
  2. Schema Design: Tenant Table
  3. Schema Refactoring: Add Tenant ID to All Tables
  4. Unique Constraint Updates
  5. Index Strategy (tenant_id + entity_id)
  6. ORM Configuration (tenant filtering in queries)
  7. Data Migration: Schema Changes (downtime planning)
  8. Data Migration: Historical Data (backfill tenant IDs)
  9. Authentication & Tenant Context
  10. API Middleware (tenant isolation enforcement)
  11. Admin Tools (tenant management)
  12. Testing: Unit Tests
  13. Testing: Integration Tests
  14. Testing: Multi-Tenant Isolation Tests
  15. Rollout Strategy (phased, with rollback plan)
- Team Collaboration Plan (which teams affected)
- Performance Impact Analysis
- Compliance Considerations (data isolation for regulatory requirements)
- Monitoring & Validation Plan
- Detailed Timeline with Milestones
- Alternative Approaches (with trade-offs)

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Multi-tenancy model justified (why isolated vs shared schema)
- [ ] Complete schema migration steps provided
- [ ] Data migration approach handles 1M+ records
- [ ] Downtime minimization strategy included
- [ ] Rollback plan included
- [ ] All modules identified that need tenant filtering
- [ ] ORM integration patterns clear
- [ ] Isolation testing comprehensive (no data leakage)
- [ ] Performance impact assessed (index strategy, query optimization)
- [ ] Compliance implications addressed
- [ ] Team collaboration details provided
- [ ] Detailed timeline with realistic estimates
- [ ] Monitoring/alerting setup included
- [ ] Multiple alternatives presented with trade-offs

---

### IP-3.2: Complex Architecture - Monolith to Microservices Refactoring

**Scenario ID:** IP-3.2
**Depth Level:** DEEP
**Type:** Refactor - Architecture Transformation
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A mature monolithic Rails application (5+ years, ~1000 files, 50+ models) needs to split into microservices. ~15 engineers, complex data dependencies, high traffic (1000 requests/sec). Business-critical payment and user management modules.

**Requirement:**
"Refactor monolithic Rails application into microservices architecture (Users, Payments, Orders services)"

**Input Contract:**
```json
{
  "requirement": "Refactor monolithic Rails application into microservices",
  "mode": "implementation",
  "depth": "DEEP",
  "repo_root": "/test-repos/rails-monolith",
  "issue_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary
- Architectural Analysis (current monolith structure)
- Microservices Decomposition Strategy
  - Service Boundaries (Users, Payments, Orders)
  - Data Ownership (which service owns which tables)
  - Communication Patterns (sync/async, API gateways)
- Risk Assessment (distributed systems complexity, data consistency)
- Detailed Tech Stack Changes
  - Service frameworks (Rails, Go, Node for specific services)
  - Message queues (RabbitMQ, Kafka)
  - Service discovery
  - Monitoring across services
- Dependency Analysis (inter-service call patterns)
- Team Structure Changes (which teams own which services)
- 15-20 Section Surgical Prompts:
  1. Service Boundary Definition
  2. Database Decomposition Strategy
  3. Service 1 (Users) Schema & Setup
  4. Service 2 (Payments) Schema & Setup
  5. Service 3 (Orders) Schema & Setup
  6. API Gateway Implementation
  7. Synchronous Communication (REST/gRPC)
  8. Asynchronous Communication (Message Queue)
  9. Data Consistency Strategy (eventual consistency)
  10. Authentication Across Services (shared token validation)
  11. Deployment Infrastructure (Docker, orchestration)
  12. Service Discovery
  13. Logging & Observability (distributed tracing)
  14. Monitoring & Alerting (per-service)
  15. Testing: Unit Tests
  16. Testing: Service Integration Tests
  17. Testing: End-to-End Tests
  18. CI/CD Pipeline Changes
  19. Rollout Strategy (incremental service extraction)
  20. Rollback & Recovery Plan
- Alternative Approaches (monolith with better modularity, strangler pattern timing)
- Team Coordination Plan
- Detailed 12-18 Month Timeline
- Cost-Benefit Analysis
- Monitoring & Validation Checkpoints

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Service boundaries clearly justified
- [ ] Data ownership model defined
- [ ] All current functionality mapped to services
- [ ] Communication patterns (sync/async) chosen with rationale
- [ ] Message queue strategy detailed
- [ ] Data consistency approach documented
- [ ] Service discovery mechanism chosen
- [ ] Observability plan (logging, tracing, monitoring)
- [ ] Testing strategy covers integration between services
- [ ] Deployment strategy realistic for team size
- [ ] Timeline realistic (12-18+ months typical)
- [ ] Rollback plan addresses distributed state
- [ ] Alternative approaches presented with trade-offs
- [ ] Team structure changes identified
- [ ] Cost implications discussed

---

### IP-3.3: Critical Bug Fix - Race Condition in Payment System

**Scenario ID:** IP-3.3
**Depth Level:** DEEP
**Type:** Fix - Critical Production Issue
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A fintech application (Node.js + PostgreSQL) has a race condition where concurrent payment requests on the same user account sometimes process twice, creating double charges. ~400 files, high-complexity concurrent code, already has monitoring. Regulatory implications (financial institution).

**Requirement:**
"Fix race condition in payment processing that causes duplicate charges in concurrent requests"

**Input Contract:**
```json
{
  "requirement": "Fix race condition in payment processing causing duplicate charges",
  "mode": "implementation",
  "depth": "DEEP",
  "repo_root": "/test-repos/fintech-payments",
  "issue_type": "bug",
  "severity": "critical",
  "github_issue": "https://github.com/org/repo/issues/892"
}
```

**Expected Output Structure:**
- Executive Summary (critical: duplicate charges, regulatory impact)
- Forensic Analysis
  - Reproduction steps
  - Root cause analysis
  - Affected transactions (estimated)
  - Financial impact
- Regulatory & Compliance Implications
- Tech Stack Analysis (Node, database, libraries)
- Current Payment Flow (diagram)
- Race Condition Details
  - Timeline of concurrent requests
  - Database transaction isolation level
  - Application-level locking gaps
- 15-20 Section Surgical Prompts:
  1. Bug Reproduction & Monitoring
  2. Root Cause Investigation (code walkthrough)
  3. Database-Level Fix (transactions, locks)
  4. Application-Level Locking (distributed locks)
  5. Idempotency Keys (prevent duplicate processing)
  6. Database Transaction Isolation Review
  7. Serialization Implementation
  8. Logging Enhancement (audit trail)
  9. Testing: Race Condition Reproduction
  10. Testing: Concurrent Payment Tests
  11. Testing: Idempotency Verification
  12. Testing: Edge Cases (timeout, partial failure)
  13. Code Review Checklist (concurrency safety)
  14. Performance Impact Assessment
  15. Refund Logic (handle past incidents)
  16. Customer Notification (if applicable)
  17. Monitoring & Alerting Enhancement
  18. Rollout Strategy (phased with verification)
  19. Verification Checklist (post-deployment)
  20. Long-term Prevention (design patterns)
- Concurrency Pattern Guide (best practices)
- Detailed Post-Incident Review Template
- Timeline for Fix Deployment
- Communication Plan (customers, regulators)

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Root cause accurately identified and explained
- [ ] Forensic analysis includes transaction logs
- [ ] Regulatory/compliance implications addressed
- [ ] Multiple fix approaches presented (locking, idempotency, transactions)
- [ ] Database transaction isolation level recommendations
- [ ] Distributed locking strategy if needed
- [ ] Idempotency keys explained with implementation
- [ ] Testing covers concurrent scenarios thoroughly
- [ ] Audit trail logging included
- [ ] Performance impact assessed
- [ ] Refund/correction logic for historical transactions
- [ ] Monitoring enhanced to detect similar issues
- [ ] Rollout is incremental with verification
- [ ] Long-term prevention mechanisms identified
- [ ] Communication plan for stakeholders

---

## Validation Checklist (All IP Scenarios)

After running each scenario, verify:

### Output Structure
- [ ] Executive summary present and accurate
- [ ] Tech stack correctly identified
- [ ] Task breakdown is actionable (not vague)
- [ ] All promised sections present
- [ ] Surgical prompts are detailed enough to follow

### Content Quality
- [ ] Recommendations follow codebase conventions (if detected)
- [ ] Framework-specific guidance provided (Rails, Express, Django, etc.)
- [ ] Security best practices included
- [ ] Testing approach is comprehensive
- [ ] Error handling mentioned

### Depth Adherence
- [ ] QUICK: 4-5 sections, ~5-10 tasks, concise
- [ ] STANDARD: 8-10 sections, ~10-15 tasks, detailed
- [ ] DEEP: 15+ sections, 20+ tasks, comprehensive with alternatives

### Safety & Risk
- [ ] Breaking changes identified (for refactoring/migration)
- [ ] Rollout strategy mitigates risk
- [ ] Testing verifies correctness
- [ ] Rollback plan exists

### Metrics
- [ ] Execution time within ±30% of estimate
- [ ] Token usage within ±20% of budget
- [ ] Framework detection accurate
- [ ] No PII or secrets in output
