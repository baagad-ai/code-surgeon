# Discovery Mode Test Scenarios

**Mode:** Discovery Mode (D)
**File:** 02-discovery-mode.md
**Total Scenarios:** 24 (8 per depth level)
**Purpose:** Test codebase analysis, architecture discovery, and risk identification across QUICK, STANDARD, and DEEP depths

---

## QUICK Depth (5 minutes, ~30K tokens)

### D-1.1: Onboarding - React SPA Analysis

**Scenario ID:** D-1.1
**Depth Level:** QUICK
**Type:** Onboarding / New Developer
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A new developer is joining a team that maintains a React single-page application. They need a quick overview of the tech stack, main modules, and entry points. ~150 files, standard React structure.

**Requirement:**
"Analyze this React SPA to understand the architecture, frameworks, and key modules"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "QUICK",
  "repo_root": "/test-repos/react-spa",
  "audience": "junior-developer"
}
```

**Expected Output Structure:**
- Tech Stack Overview (1 section, 5-6 items)
  - Primary framework: React 18.2.0
  - Language: TypeScript
  - Build tool: Webpack/Vite
  - Testing: Jest, React Testing Library
  - Styling: CSS-in-JS or CSS Modules
- Main Modules (brief list, 5-8 modules)
  - /src/components (UI components)
  - /src/pages (page-level components)
  - /src/hooks (custom hooks)
  - /src/services (API calls)
  - /src/state (state management)
- Entry Points (2-3 main files)
  - src/index.tsx
  - src/App.tsx
- Key Patterns Detected (2-3, high-level)
  - Component composition patterns
  - State management approach (Redux, Context, Zustand, etc.)
  - API communication pattern

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Framework and version correctly identified
- [ ] Main module structure clear (5-8 modules listed)
- [ ] Entry point identified (main.ts, index.tsx, etc.)
- [ ] At least 2-3 patterns detected
- [ ] Output uses ≤2 new technical terms per section without explanation
- [ ] No deep architectural details (QUICK = surface-level only)
- [ ] Duration is 5 minutes or less
- [ ] Framework detection confidence >90%

---

### D-1.2: Onboarding - Express Backend Analysis

**Scenario ID:** D-1.2
**Depth Level:** QUICK
**Type:** Onboarding / New Developer
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A new backend engineer needs to understand an Express API server. ~120 files, REST API, middleware-heavy, database integration.

**Requirement:**
"Analyze this Express API to understand the tech stack, route structure, and middleware chain"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "QUICK",
  "repo_root": "/test-repos/express-api",
  "audience": "junior-developer"
}
```

**Expected Output Structure:**
- Tech Stack Overview
  - Runtime: Node.js 18.x
  - Framework: Express 4.18.2
  - Database: PostgreSQL + Sequelize ORM
  - Language: TypeScript
  - Testing: Jest, Supertest
- API Structure (2-3 main sections)
  - Routes organization
  - Controller pattern
  - Middleware chain
- Main Endpoints (5-10 key endpoints listed)
  - POST /api/auth/login
  - GET /api/users
  - etc.
- Middleware Stack (3-5 key middleware)
  - Authentication
  - Logging
  - Error handling
- Database Schema Overview (brief, 3-4 tables)

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Framework and version correct
- [ ] Route structure clear and organized (5-10 endpoints listed)
- [ ] Middleware chain visible (3-5 middleware identified)
- [ ] Database connection identified (ORM and DB type)
- [ ] Key endpoints listed (enough to understand API scope: ≥5 endpoints)
- [ ] Uses simple language: no framework jargon beyond Express/Node basics
- [ ] QUICK depth: ≤5 sections, no implementation details
- [ ] All claims match codebase (spot-checked critical endpoints)

---

## STANDARD Depth (15 minutes, ~60K tokens)

### D-2.1: Audit - Django + React Monorepo

**Scenario ID:** D-2.1
**Depth Level:** STANDARD
**Type:** Full Audit / Architectural Assessment
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A company is assessing whether to hire a team to maintain a Django + React monorepo. They need a comprehensive architectural audit, risk identification, and pattern detection. ~500 files (250 frontend, 250 backend), complex data model, custom auth.

**Requirement:**
"Perform a comprehensive audit of this Django + React monorepo: architecture, patterns, dependencies, and risks"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "STANDARD",
  "repo_root": "/test-repos/django-react-monorepo",
  "audience": "tech-lead"
}
```

**Expected Output Structure:**
- Executive Summary (3-4 sentences: overall health, maturity level, key risks)
- Tech Stack Analysis
  - Frontend: React 18.x, build tool, styling approach
  - Backend: Django 4.x, DRF version, database
  - Shared: monorepo structure, CI/CD approach
  - Testing: coverage, frameworks used
  - DevOps: deployment, infrastructure
- Architecture Overview (text or ASCII diagram)
  - Monorepo organization (folder structure)
  - Frontend architecture (components, state management)
  - Backend architecture (Django apps, API structure)
  - Data flow between layers
  - External integrations
- Module/Component Breakdown (5-8 major modules)
  - Authentication system
  - User management
  - Core business logic
  - etc.
- Identified Patterns (5-8 patterns)
  - Design patterns (Singleton, Factory, etc.)
  - Architectural patterns (MVC, layering)
  - Framework-specific patterns
  - Data management patterns
  - Testing patterns
- Dependencies & Relationships
  - Frontend dependencies (top 5-10)
  - Backend dependencies (top 5-10)
  - Internal module dependencies
- Team Conventions (4-6 patterns detected)
  - Naming conventions
  - Code organization
  - Error handling approach
  - Testing approach
- Risk Assessment (3-5 risks identified)
  - Technical debt areas
  - Testing gaps
  - Security concerns
  - Performance bottlenecks
  - Scaling limitations
- Opportunities (3-5 improvements suggested)
  - Code quality improvements
  - Performance optimizations
  - Testing enhancement
  - Documentation needs

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] All major modules identified (≥5 modules per layer)
- [ ] Architecture documented (diagram or clear hierarchical description)
- [ ] At least 5-8 distinct patterns detected with examples
- [ ] Dependencies mapped with version numbers (≥10 key deps)
- [ ] Team conventions identified (≥4 conventions: naming, structure, error handling, testing)
- [ ] Risk assessment comprehensive (≥3 risks with impact assessment)
- [ ] Opportunities are actionable (specific file/module references)
- [ ] Output length: STANDARD <1500 lines (vs QUICK <500 lines)
- [ ] Code snippets for complex patterns included (≥2 examples)
- [ ] Both frontend and backend analysis present

---

### D-2.2: Audit - Rails Application

**Scenario ID:** D-2.2
**Depth Level:** STANDARD
**Type:** Full Audit / Rails Framework Knowledge
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Rails application (5+ years old, ~1000 files) needs assessment. Business acquisition happening; due diligence requires architectural understanding. Uses Rails conventions but has custom modifications.

**Requirement:**
"Audit Rails application: framework conventions, data models, patterns, and architecture"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "STANDARD",
  "repo_root": "/test-repos/rails-app",
  "audience": "cto"
}
```

**Expected Output Structure:**
- Executive Summary
  - Rails version and maturity
  - Overall code quality assessment
  - Key architectural strengths and weaknesses
- Rails Stack Analysis
  - Framework version (Rails 6.x, 7.x)
  - Ruby version
  - Database (PostgreSQL, MySQL)
  - View layer (ERB, Slim)
  - Testing setup (RSpec, Minitest)
  - CI/CD pipeline
- Data Model Overview
  - Core entities (users, products, orders, etc.)
  - Relationships (associations)
  - Validations approach
  - Callback usage
- Architecture Pattern Analysis
  - Convention adherence (how well follows Rails conventions)
  - Service layer usage (if present)
  - Job queuing (background jobs)
  - Caching strategy
- Rails-Specific Patterns
  - ActiveRecord usage patterns
  - Scopes and query optimization
  - Devise/Pundit integration (auth/authorization)
  - View components strategy
  - Validation patterns
- Test Coverage Assessment
  - Unit test coverage
  - Integration test approach
  - Feature/system test coverage
  - Gaps identified
- Gem Dependency Analysis
  - Core gems
  - Outdated or deprecated gems
  - Security vulnerabilities
- Configuration Management
  - Environment setup
  - Secrets management
  - Feature flags
- Risk Assessment
  - Legacy code areas
  - Technical debt
  - Security concerns
  - Performance bottlenecks
  - Testing gaps

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Rails version and conventions understood (versions for Rails + Ruby)
- [ ] Data model clearly documented (≥5 entities mapped)
- [ ] All major entities and relationships mapped (≥10 associations)
- [ ] At least 5-8 distinct patterns identified with examples
- [ ] Test coverage assessment realistic (coverage % estimated)
- [ ] Gem dependencies analyzed (≥15 gems reviewed, outdated flagged)
- [ ] Risk assessment comprehensive (≥3 specific risks with remediation)
- [ ] Opportunities for improvement identified (≥3 specific recommendations)
- [ ] Rails-specific conventions highlighted (Active Record, scopes, callbacks)

---

### D-2.3: Assessment - Go Microservice

**Scenario ID:** D-2.3
**Depth Level:** STANDARD
**Type:** Technical Assessment / New Service Analysis
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A team wrote a Go microservice and wants to verify it's production-ready. Assessment needed for: architecture, error handling, testing, observability. ~80 files, gRPC-based service.

**Requirement:**
"Assess Go microservice: framework, dependencies, interfaces, test coverage, and production readiness"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "STANDARD",
  "repo_root": "/test-repos/go-microservice",
  "audience": "devops-engineer"
}
```

**Expected Output Structure:**
- Executive Summary
  - Service purpose
  - Production-readiness assessment
  - Key strengths and gaps
- Tech Stack
  - Go version
  - gRPC/REST framework
  - Database driver (if any)
  - Key dependencies
  - Container setup (Docker)
- Service Architecture
  - Entry point and initialization
  - Main components (handlers, services, repositories)
  - Configuration management
  - Dependency injection approach
- Interface Definitions
  - gRPC services (proto files)
  - REST endpoints (if any)
  - Data models
  - Error codes
- Go-Specific Patterns
  - Error handling (custom errors, error wrapping)
  - Concurrency patterns (goroutines, channels)
  - Interface usage (implicit interfaces)
  - Package organization
- Testing Coverage
  - Unit tests present
  - Integration test approach
  - Mocking strategy
  - Test utilities
- Error Handling
  - Panic recovery
  - Error propagation
  - Logging integration
  - Graceful shutdown
- Observability
  - Logging approach
  - Metrics (Prometheus, etc.)
  - Distributed tracing
  - Health check endpoints
- Deployment & Operations
  - Docker configuration
  - Environment variables
  - Graceful restart handling
  - Resource limits
- Security
  - gRPC/TLS setup
  - Authentication/authorization
  - Input validation
  - Secrets management
- Performance
  - Connection pooling
  - Resource usage patterns
  - Concurrency limits

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Go version and framework identified
- [ ] Service purpose clear
- [ ] gRPC services documented
- [ ] Error handling strategy clear
- [ ] Testing coverage assessed
- [ ] Observability setup evaluated
- [ ] Production-readiness checklist provided
- [ ] At least 5-8 patterns identified
- [ ] Deployment considerations included

---

### D-2.4: Assessment - Rust CLI Application

**Scenario ID:** D-2.4
**Depth Level:** STANDARD
**Type:** Technical Assessment / Language-Specific Analysis
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Rust CLI tool (command-line application) is being evaluated. Assessment needed for: code quality, error handling, dependencies, and production maturity. ~150 files, uses many libraries.

**Requirement:**
"Assess Rust CLI application: language patterns, error handling, dependencies, and code quality"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "STANDARD",
  "repo_root": "/test-repos/rust-cli",
  "audience": "rust-team-lead"
}
```

**Expected Output Structure:**
- Executive Summary
  - Purpose of CLI
  - Code maturity level
  - Key strengths and areas for improvement
- Rust Stack
  - Rust edition (2021, 2018)
  - Cargo workspace structure
  - Key dependencies (clap, serde, tokio, etc.)
  - MSRV (minimum supported Rust version)
- Project Structure
  - Binary crates vs library crates
  - Module organization
  - Main entry point and initialization
- Rust-Specific Patterns
  - Error handling (Result, custom errors, anyhow vs thiserror)
  - Ownership and borrowing patterns
  - Trait usage
  - Lifetime management
  - Async/await usage (if applicable)
  - Macro usage
- Command-Line Interface
  - Argument parsing approach (clap, structopt)
  - Command hierarchy
  - Help text quality
  - Shell completions
- Error Handling
  - Error types used
  - Error messages clarity
  - Exit codes
  - Recovery strategies
- Testing Coverage
  - Unit test presence
  - Integration tests
  - Test utilities (fixtures, helpers)
  - Coverage gaps
- Dependencies Analysis
  - Dependency tree
  - Security audit (cargo-audit)
  - License compliance
  - Unused dependencies
- Performance
  - Compilation time
  - Binary size
  - Runtime performance patterns
  - Concurrency patterns
- Code Quality
  - Clippy warnings
  - Format compliance (rustfmt)
  - Documentation comments
  - Examples
- Deployment & Distribution
  - Build configuration
  - Cross-compilation support
  - Release process
  - Installation methods

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Rust version and edition identified
- [ ] Crate structure clearly mapped
- [ ] Error handling strategy clear
- [ ] Dependencies analyzed (including security)
- [ ] CLI interface documented
- [ ] Testing coverage assessed
- [ ] Code quality metrics included
- [ ] At least 5-8 Rust-specific patterns identified
- [ ] Performance considerations included

---

## DEEP Depth (30 minutes, ~90K tokens)

### D-3.1: Full Audit - Legacy Node.js Application (10+ years)

**Scenario ID:** D-3.1
**Depth Level:** DEEP
**Type:** Comprehensive Legacy System Analysis
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A Node.js application built 10+ years ago (pre-ES6, callback-heavy) is being evaluated for modernization. Huge codebase (~2000 files), business-critical, low test coverage. Team wants roadmap for gradual modernization.

**Requirement:**
"Perform comprehensive audit of legacy Node.js application: full context, tech debt, patterns, and modernization roadmap"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "DEEP",
  "repo_root": "/test-repos/legacy-nodejs",
  "audience": "engineering-leadership"
}
```

**Expected Output Structure:**
- Executive Summary (4-5 sentences with context)
  - Application maturity and age
  - Overall assessment
  - Primary risks and opportunities
  - Modernization recommendation
- Historical Context & Evolution
  - Original architecture (circa 2010s)
  - Major transitions (Node versions, framework changes)
  - Key decisions and their implications
  - Current state
- Tech Stack Deep Dive
  - Node.js version (old, EOL?)
  - Framework (Express, custom, etc.)
  - Database (MySQL, MongoDB, etc.)
  - Middleware and dependencies (100+ dependencies?)
  - Testing infrastructure (or lack thereof)
  - Build and deployment
- Architecture Analysis (detailed)
  - Application layers
  - Module organization
  - Data flow
  - External integrations
  - Monolithic vs modular assessment
- Code Quality Assessment
  - Callback hell / Promise migration status
  - Async/await adoption
  - Error handling patterns (old vs new)
  - Naming conventions consistency
  - Code duplication analysis
  - Complexity metrics
- Design Patterns (8-15 patterns identified)
  - Architectural patterns
  - Design patterns (valid and anti-patterns)
  - Framework-specific patterns
  - Evolution of patterns over time
  - Anti-patterns identified
- Testing Landscape
  - Unit test coverage (real percentage)
  - Integration test approach
  - Gaps and fragile tests
  - Test infrastructure issues
  - Recommendations for improvement
- Dependencies Analysis (deep)
  - Dependency tree (major dependencies only)
  - Outdated packages and security vulnerabilities
  - Deprecated packages
  - Bloat analysis (unnecessary dependencies)
  - Migration costs for key dependencies
- Technical Debt Mapping
  - Debt areas (by module/function)
  - Severity assessment (critical, high, medium)
  - Impact on new features
  - Estimated remediation effort
  - Compound interest (debt growing costs)
- Security Assessment
  - Known vulnerabilities
  - Security patterns (or lack thereof)
  - Data handling risks
  - Authentication/authorization audit
  - Secrets management
- Performance Analysis
  - Bottlenecks identified
  - Memory usage patterns
  - Request handling efficiency
  - Database query optimization opportunities
  - Caching gaps
- Modernization Roadmap (phased)
  - Phase 1 (Months 1-3): Quick wins (dependencies, obvious bugs)
  - Phase 2 (Months 4-6): Testing infrastructure, async/await migration
  - Phase 3 (Months 7-12): Module reorganization, framework upgrade
  - Phase 4 (Months 12-24): Full modernization, microservices consideration
  - Effort estimates per phase
  - Risk assessment per phase
- Team & Knowledge Management
  - Knowledge concentration risks
  - Documentation gaps
  - Onboarding difficulty
  - Training needs
- Business Impact Assessment
  - Risk of not modernizing
  - Cost of modernization
  - Timeline to modern state
  - Recommendation (modernize, rewrite, migrate gradually)
- Alternative Approaches
  - Rewrite from scratch (costs/benefits)
  - Strangler pattern (incremental replacement)
  - Containerization as band-aid
  - Managed hosting options

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Historical context understood and documented (major version milestones identified)
- [ ] All major modules identified (≥20 modules broken down)
- [ ] Tech debt comprehensively mapped (≥10 debt areas with severity)
- [ ] 10+ distinct patterns identified (including anti-patterns and code examples)
- [ ] Testing gaps clearly identified (current coverage % and gaps listed)
- [ ] Security vulnerabilities listed with severity (≥5 issues identified)
- [ ] Performance bottlenecks documented with specific locations
- [ ] Modernization roadmap is phased and realistic (4+ phases with timelines)
- [ ] Effort estimates provided per phase (in weeks/months)
- [ ] Alternative approaches evaluated (≥2 approaches compared)
- [ ] Knowledge silos identified and addressed
- [ ] Output length: DEEP <2000 lines (vs STANDARD <1500 lines)
- [ ] Actionable next steps provided (specific file/module references)

---

### D-3.2: Full Audit - Multi-Language Platform

**Scenario ID:** D-3.2
**Depth Level:** DEEP
**Type:** Comprehensive Multi-Stack Analysis
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A technology platform spans 4+ languages (Python backend, Go services, React frontend, Rust utilities) with ~1500 files total. Complex inter-service communication, legacy integrations. Seeking to understand full topology and communication patterns.

**Requirement:**
"Analyze multi-language platform: all stacks, inter-service communication, boundaries, and integration patterns"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "DEEP",
  "repo_root": "/test-repos/multi-language-platform",
  "audience": "solution-architect"
}
```

**Expected Output Structure:**
- Executive Summary
  - Platform overview
  - Technology diversity rationale
  - Integration complexity level
  - Key strengths and risks
- Platform Architecture (diagram/text)
  - High-level topology
  - Service boundaries
  - Data flow
  - Integration points
- Python Backend Component
  - Framework (Django, FastAPI, etc.)
  - Major modules
  - Database interactions
  - Patterns and conventions
  - Testing approach
  - Performance characteristics
- Go Microservices Component
  - Service count and purposes
  - Communication protocols (gRPC, HTTP)
  - Data models
  - Deployment strategy
  - Scaling characteristics
- React Frontend Component
  - Architecture (state management, component structure)
  - API integration (how it calls Python/Go)
  - Performance characteristics
  - Testing approach
- Rust Utilities Component
  - Purpose and scope
  - Integration points
  - Performance role (speed-critical functions)
  - Deployment model
- Inter-Service Communication
  - Synchronous APIs (REST, gRPC)
  - Asynchronous communication (queues, events)
  - Service discovery mechanism
  - Error handling across services
  - Timeout and retry strategies
  - Load balancing
- Data Management
  - Shared database vs isolated databases
  - Data consistency approach (ACID vs eventual consistency)
  - Data migration patterns (if any)
  - Cache layers
  - Data synchronization
- Observability & Monitoring
  - Logging approach (centralized?)
  - Metrics collection (Prometheus, DataDog, etc.)
  - Distributed tracing (Jaeger, Zipkin)
  - Alerting strategy
  - Service health monitoring
- Development Workflow
  - Team structure (Python team, Go team, etc.)
  - Local development setup
  - Testing strategy (unit, integration, E2E)
  - Deployment pipeline
  - Rollback procedures
- Deployment & Infrastructure
  - Container orchestration (Kubernetes, Docker Compose)
  - Service configuration
  - Environment management (dev, staging, prod)
  - Scaling strategy (horizontal, vertical)
  - Disaster recovery
- Security & Compliance
  - Authentication/authorization (across services)
  - Service-to-service authentication
  - Data encryption (in transit, at rest)
  - Secrets management
  - Compliance requirements
- Performance & Optimization
  - Bottleneck identification (per component)
  - Optimization opportunities
  - Caching strategy
  - Resource utilization
  - Scaling limits
- Operational Challenges
  - Complexity of multi-stack (dev hiring, knowledge silos)
  - Dependency version management
  - Language upgrade paths
  - Build and deployment time
  - Debugging across services
- Future Evolution
  - Technology debt
  - Service mesh consideration
  - Modernization opportunities
  - Consolidation opportunities (reduce language count)

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] All 4+ languages and stacks identified
- [ ] Service boundaries clearly defined
- [ ] Inter-service communication protocols documented
- [ ] Data consistency model explained
- [ ] Observability approach comprehensive
- [ ] Deployment topology clear
- [ ] Security across service boundaries addressed
- [ ] Performance bottlenecks identified
- [ ] Operational challenges acknowledged
- [ ] Future evolution recommendations provided
- [ ] Output is comprehensive and actionable

---

### D-3.3: Risk Assessment - Complex SaaS Platform

**Scenario ID:** D-3.3
**Depth Level:** DEEP
**Type:** Comprehensive Risk & Compliance Assessment
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A SaaS platform (React + Node.js + PostgreSQL) handles sensitive customer data and payment processing. GDPR, SOC 2 compliance required. ~800 files, mature codebase. Seeking comprehensive risk assessment for compliance audit.

**Requirement:**
"Risk assessment of SaaS platform: security, compliance, data handling, and vulnerability mapping"

**Input Contract:**
```json
{
  "mode": "discovery",
  "depth": "DEEP",
  "repo_root": "/test-repos/saas-platform",
  "audience": "security-officer"
}
```

**Expected Output Structure:**
- Executive Summary
  - Compliance readiness (GDPR, SOC 2, etc.)
  - Overall risk level (low, medium, high)
  - Critical vulnerabilities (if any)
  - Recommendation
- Compliance Framework Assessment
  - GDPR compliance status
    - Data subject rights implementation
    - Consent management
    - Data processing agreements
    - Breach notification capability
  - SOC 2 compliance status
    - Access controls
    - Change management
    - System monitoring
    - Incident response
  - Other requirements (PCI-DSS for payments, HIPAA, etc.)
- Security Posture
  - Authentication mechanisms
    - Implementation (JWT, sessions, OAuth)
    - Strength assessment
    - MFA support
    - Session management
  - Authorization
    - Role-based access control (RBAC)
    - Permission model
    - Privilege escalation risks
  - Encryption
    - Data at rest (database encryption)
    - Data in transit (TLS/HTTPS)
    - Key management
    - Encryption gaps
- Data Security
  - Sensitive data identification
    - Customer data
    - Payment data (PCI compliance)
    - Authentication credentials
    - API keys and secrets
  - Data handling
    - Storage security
    - Transmission security
    - Retention policies
    - Deletion procedures
  - Data loss prevention
    - Backup procedures
    - Disaster recovery plan
    - Data recovery testing
- Vulnerability Assessment
  - Known vulnerabilities (CVE scan results)
  - Dependency vulnerabilities
  - OWASP Top 10 assessment
    - SQL injection risk
    - XSS risk
    - CSRF protection
    - Insecure deserialization
    - etc.
  - Infrastructure vulnerabilities
  - Configuration vulnerabilities
- Access Control
  - User authentication audit
  - Administrative access control
  - Service-to-service authentication
  - API authentication
  - Secrets management (how are API keys, DB passwords stored)
- Logging & Audit
  - Audit logging implementation
    - User actions logged
    - Administrative changes logged
    - Security event logging
  - Log retention
  - Log analysis capabilities
  - Alerting on security events
- Incident Response
  - Incident response plan (documented?)
  - Security team structure
  - Breach notification procedures
  - Post-incident review process
  - Penetration testing history
- Third-Party Risk
  - External service dependencies
  - SaaS dependencies (security posture)
  - Integration risks
  - Vendor compliance status
- Business Continuity
  - Availability commitment (SLA)
  - Disaster recovery plan
  - Business continuity testing
  - Failover mechanisms
  - Data backup testing
- Risk Prioritization
  - Critical risks (must fix immediately)
  - High risks (fix before next release)
  - Medium risks (roadmap item)
  - Low risks (monitor/enhance)
  - Estimated remediation effort for each
- Remediation Roadmap
  - Immediate actions (week 1)
  - Near-term (1-3 months)
  - Medium-term (3-6 months)
  - Long-term (6-12 months)
  - Resource requirements
- Recommendations
  - Quick wins
  - Architectural improvements
  - Process improvements
  - Training needs
  - Third-party assessment (penetration testing, SOC 2 audit)

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] All compliance frameworks addressed
- [ ] Security posture comprehensively assessed
- [ ] Data handling practices documented
- [ ] Vulnerabilities identified and prioritized
- [ ] Access control mechanisms evaluated
- [ ] Incident response capability assessed
- [ ] Business continuity planning evaluated
- [ ] Risk prioritization clear
- [ ] Remediation roadmap is phased and realistic
- [ ] Actionable recommendations provided
- [ ] Compliance-ready for audit
- [ ] Output is comprehensive and audit-quality

---

## Validation Checklist (All Discovery Scenarios)

After running each scenario, verify:

### Output Structure
- [ ] Executive summary present and accurate
- [ ] Tech stack correctly identified
- [ ] Architecture clearly explained
- [ ] At least 3-5 patterns identified (more for DEEP)
- [ ] All promised sections present

### Content Quality
- [ ] Framework detection accurate (>90% confidence)
- [ ] Module organization clear
- [ ] Risk assessment appropriate to depth level
- [ ] Patterns are framework-specific where applicable
- [ ] Opportunities are actionable

### Depth Adherence
- [ ] QUICK: 3-4 sections, high-level overview, 2-3 patterns
- [ ] STANDARD: 8-10 sections, comprehensive, 5-8 patterns
- [ ] DEEP: 15+ sections, exhaustive, 10+ patterns with context

### Audience Appropriateness
- [ ] Output language matches audience (junior vs architect)
- [ ] Technical depth appropriate
- [ ] Business context included (where relevant)
- [ ] Actionable for audience role

### Metrics
- [ ] Execution time within ±30% of estimate
- [ ] Token usage within ±20% of budget
- [ ] Framework detection accurate
- [ ] No PII or secrets in output
- [ ] Duration appropriate to depth level
