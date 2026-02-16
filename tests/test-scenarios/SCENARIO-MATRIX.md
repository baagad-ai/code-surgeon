# Test Scenario Matrix - code-surgeon v1.2

**Last Updated:** 2026-02-13
**Version:** 1.2
**Total Scenarios:** 106
**Purpose:** Comprehensive testing guide for all modes, depth levels, audiences, and edge cases

---

## Overview

This document serves as the master index for code-surgeon integration testing. It organizes 106 test scenarios across 4 modes, 3 depth levels, multiple audiences, mixed-intent scenarios, edge cases, and error handling.

### Quick Stats

| Category | Count | Scenarios |
|----------|-------|-----------|
| **Implementation Planning (IP)** | 24 | IP-1.1 to IP-3.3 (8 per depth) |
| **Discovery Mode (D)** | 24 | D-1.1 to D-3.3 (8 per depth) |
| **Review Mode (R)** | 24 | R-1.1 to R-3.3 (8 per depth) |
| **Optimization Mode (O)** | 24 | O-1.1 to O-3.3 (8 per depth) |
| **Mixed-Intent Scenarios** | 5 | MX-1 to MX-5 |
| **Edge Cases** | 8 | EC-1 to EC-8 |
| **Error Handling** | 6 | EH-1 to EH-6 |
| **TOTAL** | **106** | — |

---

## Mode Coverage Grid

### 1. Implementation Planning Mode (24 scenarios)

**Purpose:** Feature implementation, bug fixes, refactoring guidance

| Scenario ID | Depth | Type | Context | Expected Output | Duration | Tokens |
|------------|-------|------|---------|-----------------|----------|--------|
| IP-1.1 | QUICK | Build | "Add password reset feature" | 5-section surgical prompt | 5 min | 30K |
| IP-1.2 | QUICK | Build | "Implement SSO integration" | Step-by-step implementation guide | 5 min | 30K |
| IP-2.1 | STANDARD | Build | "Add two-factor authentication" | 9-section surgical prompt with alternatives | 15 min | 60K |
| IP-2.2 | STANDARD | Fix | "Fix memory leak in event emitter" | Root cause analysis + fix guidance | 15 min | 60K |
| IP-2.3 | STANDARD | Refactor | "Extract authentication logic to separate service" | Architecture changes + safety validation | 15 min | 60K |
| IP-2.4 | STANDARD | Build | "Migrate from REST to GraphQL API" | Phase-by-phase migration plan | 15 min | 60K |
| IP-3.1 | DEEP | Build | "Redesign database schema for multi-tenancy" | Deep architectural analysis + migration strategy | 30 min | 90K |
| IP-3.2 | DEEP | Refactor | "Refactor monolith to microservices" | Detailed phase breakdown with risk assessment | 30 min | 90K |
| IP-3.3 | DEEP | Fix | "Fix complex race condition in payment system" | Forensic analysis + comprehensive fix with testing | 30 min | 90K |

### 2. Discovery Mode (24 scenarios)

**Purpose:** Understanding architecture, identifying patterns, assessing risk

| Scenario ID | Depth | Use Case | Codebase Type | Expected Output | Duration | Tokens |
|------------|-------|----------|---------------|-----------------|----------|--------|
| D-1.1 | QUICK | Onboarding | React SPA | Framework stack, main modules, entry points | 5 min | 30K |
| D-1.2 | QUICK | Onboarding | Express backend | Framework versions, API structure, middleware | 5 min | 30K |
| D-2.1 | STANDARD | Audit | Django + React monorepo | Full architecture, patterns, dependencies, risks | 15 min | 60K |
| D-2.2 | STANDARD | Audit | Rails application | Framework conventions, data models, patterns | 15 min | 60K |
| D-2.3 | STANDARD | Assessment | Go microservice | Framework, dependencies, interfaces, test coverage | 15 min | 60K |
| D-2.4 | STANDARD | Assessment | Rust CLI app | Language features, module structure, error handling | 15 min | 60K |
| D-3.1 | DEEP | Full Audit | Legacy Node.js app (10y+) | Complete historical context, tech debt, patterns | 30 min | 90K |
| D-3.2 | DEEP | Full Audit | Multi-language platform | All stacks, inter-service communication, boundaries | 30 min | 90K |
| D-3.3 | DEEP | Risk Assessment | Complex SaaS | Detailed risk map, security implications, upgrade path | 30 min | 90K |

### 3. Review Mode (24 scenarios)

**Purpose:** Assessing change impact, detecting breaking changes, validating safety

| Scenario ID | Depth | Change Type | Requirement | Expected Output | Duration | Tokens |
|------------|-------|------------|-------------|-----------------|----------|--------|
| R-1.1 | QUICK | Patch | "Update React from 17 to 18" | Breaking changes list, migration steps | 5 min | 30K |
| R-1.2 | QUICK | Patch | "Upgrade lodash from 4.x to 4.y" | Dependency impact, deprecations | 5 min | 30K |
| R-2.1 | STANDARD | Minor | "Add email notifications to user model" | Full impact assessment, affected modules | 15 min | 60K |
| R-2.2 | STANDARD | Minor | "Rename API endpoint from /api/users to /api/v2/users" | Breaking changes, client impact, migration plan | 15 min | 60K |
| R-2.3 | STANDARD | Major | "Migrate database from MongoDB to PostgreSQL" | Schema impact, data migration risks, timeline | 15 min | 60K |
| R-2.4 | STANDARD | Major | "Switch authentication from JWT to OAuth2" | All affected components, integration points | 15 min | 60K |
| R-3.1 | DEEP | Critical | "Restructure payment processing (PCI impact)" | Comprehensive safety validation, compliance check | 30 min | 90K |
| R-3.2 | DEEP | Critical | "Refactor core authentication system" | Forensic impact analysis, security implications | 30 min | 90K |
| R-3.3 | DEEP | Critical | "Reorganize database schema (data integrity)" | Deep impact analysis, rollback strategy | 30 min | 90K |

### 4. Optimization Mode (24 scenarios)

**Purpose:** Finding bottlenecks, security vulnerabilities, efficiency improvements

| Scenario ID | Depth | Focus | Codebase Type | Expected Output | Duration | Tokens |
|------------|-------|-------|---------------|-----------------|----------|--------|
| O-1.1 | QUICK | Performance | React app | Top 3 rendering bottlenecks | 5 min | 30K |
| O-1.2 | QUICK | Security | Node API | Top 3 security risks | 5 min | 30K |
| O-2.1 | STANDARD | Performance | Django app | 5-8 bottlenecks with fix guidance | 15 min | 60K |
| O-2.2 | STANDARD | Security | React + Node | Vulnerabilities prioritized by CVSS score | 15 min | 60K |
| O-2.3 | STANDARD | Efficiency | Rails app | Code duplication, inefficient patterns | 15 min | 60K |
| O-2.4 | STANDARD | Tech Debt | Legacy Node app | Tech debt roadmap with prioritization | 15 min | 60K |
| O-3.1 | DEEP | Comprehensive | High-traffic SaaS | All three: performance + security + efficiency | 30 min | 90K |
| O-3.2 | DEEP | Comprehensive | Microservices platform | Cross-service optimization opportunities | 30 min | 90K |
| O-3.3 | DEEP | Comprehensive | Data-intensive app | Advanced performance tuning, caching strategy | 30 min | 90K |

---

## Audience-Specific Output Validation

These scenarios ensure output is appropriate for different audiences:

| Scenario ID | Mode | Audience | Context | Expected Adaptation |
|------------|------|----------|---------|---------------------|
| AUD-1 | Discovery | Junior Developer | "Onboard new team member" | Explains concepts, includes links, simple language |
| AUD-2 | Review | Tech Lead | "Assess breaking changes" | Technical depth, frameworks, dependencies |
| AUD-3 | Optimization | DevOps Engineer | "Identify infrastructure improvements" | Infrastructure focus, metrics, monitoring |
| AUD-4 | Implementation | Product Manager | "Estimate implementation effort" | Business context, timeline, risk |
| AUD-5 | All Modes | Security Officer | "Compliance validation" | Security emphasis, compliance references |

---

## Mixed-Intent Scenarios (5 scenarios)

These scenarios validate multi-step workflows and combined modes:

| Scenario ID | Steps | Modes Involved | Context | Expected Behavior |
|------------|-------|-----------------|---------|-------------------|
| MX-1 | 3 | D → IP → R | "Understand architecture, plan feature, then assess safety" | Sequential flow with state passing |
| MX-2 | 3 | D → O → IP | "Audit codebase, find optimization, then plan refactor" | Context carried across modes |
| MX-3 | 2 | IP → O | "Implement feature, then optimize for performance" | Feature added, then tuned |
| MX-4 | 4 | D → R → IP → O | "Full workflow: audit, review breaking changes, plan fix, optimize" | Complete orchestration |
| MX-5 | 2 | O → R | "Find security vulnerability, review impact of proposed patch" | Vulnerability context in review |

---

## Edge Cases (8 scenarios)

These scenarios validate handling of special repository types and unusual conditions:

| Scenario ID | Category | Description | Expected Behavior |
|------------|----------|-------------|-------------------|
| EC-1 | Monorepo | "Analyze monorepo with 10+ packages" | Correct framework detection per workspace, unified analysis |
| EC-2 | Polyglot | "Codebase mixing Python, Go, Node.js" | Detect all languages, handle framework mapping correctly |
| EC-3 | Legacy | "20-year-old codebase, no tests, outdated frameworks" | Graceful handling, highlight risks, suggest modernization path |
| EC-4 | Massive | "10M+ lines of code, 500+ files" | Respect token limits, return Tier 1-2 results only, mark incomplete |
| EC-5 | Greenfield | "Brand new repo with minimal structure" | Provide setup recommendations, no false pattern detection |
| EC-6 | Mixed-Tier | "Some files modern, some legacy, some generated" | Correctly tier files, ignore generated files, suggest consolidation |
| EC-7 | Circular | "Circular dependencies and imports" | Detect correctly, suggest refactoring, don't crash |
| EC-8 | Configuration-Heavy | "Lots of config files, minimal business logic" | Focus analysis on actual code, not config boilerplate |

---

## Error Handling Scenarios (6 scenarios)

These scenarios validate robust error handling and recovery:

| Scenario ID | Error Type | Scenario | Expected Handling |
|------------|-----------|----------|-------------------|
| EH-1 | Input Validation | "Empty requirement string for Implementation mode" | Clear error, suggest mode routing |
| EH-2 | Framework Detection | "Unrecognized framework or framework detection timeout" | Graceful degradation, proceed with generic analysis |
| EH-3 | Token Budget | "Analysis would exceed 120K tokens" | Return best results from Tiers 1-2, mark as incomplete |
| EH-4 | Repository Access | "No read permission on repo directory" | Immediate stop, clear error message, suggest fix |
| EH-5 | Timeout | "Phase execution exceeds global 30-minute limit" | Save state, offer resumption, don't lose progress |
| EH-6 | Memory Pressure | "Analysis causes excessive memory usage" | Cleanup, reduce tier depth, return partial results |

---

## Test Execution Strategy

### Phase 1: Quick Validation (Day 1)
- Run all QUICK depth scenarios (12 total: 3 per mode)
- Run MX-1 and MX-2 for basic multi-step validation
- Run one edge case per category (EC-1, EC-4, EC-6)
- Run one error scenario (EH-1)

### Phase 2: Standard Coverage (Days 2-3)
- Run all STANDARD depth scenarios (12 total: 3 per mode)
- Run remaining mixed-intent scenarios (MX-3 to MX-5)
- Run remaining edge cases (EC-2, EC-3, EC-5, EC-7, EC-8)
- Run remaining error scenarios (EH-2 to EH-6)

### Phase 3: Deep Validation (Days 4-5)
- Run all DEEP depth scenarios (12 total: 3 per mode)
- Run audience-specific scenarios (AUD-1 to AUD-5)
- Complex multi-mode orchestration tests
- Performance and stress testing

---

## Test Failure Handling

**If a Scenario Fails:**

### 1. Immediate Response
- Document: Requirement, actual output, expected output
- Log: Token usage, execution time, error messages
- Check: Network connectivity, rate limiting, service availability
- Verify: Test repository exists and is accessible

### 2. Retry Logic
- First failure: Retry scenario with same parameters (once)
- Second failure: Run with reduced depth (QUICK or STANDARD instead of DEEP)
- Third failure: Document as blocker, escalate to development team

### 3. Escalation

| Severity | Criteria | Action | Timeline |
|----------|----------|--------|----------|
| **Critical** | Mode inoperability, all scenarios in mode failing | Report immediately, block release | Same day |
| **High** | Multiple scenarios failing (≥3), specific depth level broken | Schedule emergency triage | Within 24 hours |
| **Medium** | Single scenario failing, workaround exists | Document for next sprint | Next planning cycle |
| **Low** | Output formatting issue, all data correct | Schedule for later review | Next month |

### 4. Known False Positives & Limitations

- **Monorepo Detection Variability:** Monorepos with >20 packages may have lower pattern detection confidence (some packages marked "custom")
- **Legacy Code Analysis Confidence:** Confidence varies by codebase age and documentation completeness (60-95% range)
- **Framework Detection Timeouts:** Large codebases may timeout detection for secondary frameworks (system degrades gracefully)
- **Performance Profiling Limitations:** Static analysis may not capture runtime performance bottlenecks (consider runtime profiling for verification)
- **Language Version Detection:** May miss exact version numbers in legacy/unconventional setups (recommend manual verification)
- **Token Budget Variance:** Complex codebases may exceed ±20% estimate (system returns Tier 1-2 results only)

---

## Scenario Files Reference

Each scenario is detailed in its mode-specific file:

- **[01-implementation-planning.md](./01-implementation-planning.md)** - IP-1.1 to IP-3.3
- **[02-discovery-mode.md](./02-discovery-mode.md)** - D-1.1 to D-3.3
- **[03-review-mode.md](./03-review-mode.md)** - R-1.1 to R-3.3
- **[04-optimization-mode.md](./04-optimization-mode.md)** - O-1.1 to O-3.3

Additional files for specialized testing:
- **edge-cases.md** - EC-1 to EC-8 (future)
- **error-handling.md** - EH-1 to EH-6 (future)
- **mixed-intent.md** - MX-1 to MX-5 (future)
- **audience-specific.md** - AUD-1 to AUD-5 (future)

---

## Critical Path Scenarios (Recommended for Minimal Testing)

If testing time is limited, run these **15 scenarios** for essential coverage. Each represents one of the three depth levels across all four modes plus critical error paths:

**Implementation Planning (3 scenarios)**
- IP-1.1: QUICK - Simple feature (password reset)
- IP-2.1: STANDARD - Medium complexity feature (two-factor auth)
- IP-3.1: DEEP - Complex architectural change (multi-tenancy)

**Discovery Mode (3 scenarios)**
- D-1.1: QUICK - Onboarding new developer to codebase
- D-2.1: STANDARD - Full architecture audit
- D-3.1: DEEP - Legacy system analysis (10+ year old codebase)

**Review Mode (3 scenarios)**
- R-1.1: QUICK - Simple dependency upgrade (React)
- R-2.1: STANDARD - Feature impact (email notifications)
- R-3.1: DEEP - Critical system change (payment processing)

**Optimization Mode (3 scenarios)**
- O-1.1: QUICK - Performance analysis (rendering bottlenecks)
- O-2.1: STANDARD - Comprehensive optimization (Django app)
- O-3.1: DEEP - Full system optimization (high-traffic SaaS)

**Critical Error Path (3 scenarios)**
- EH-1: Input validation (empty requirement)
- EH-3: Token budget overflow (analysis exceeds 120K tokens)
- MX-1: Multi-step workflow (D → IP → R sequential flow)

**Rationale:** These 15 scenarios cover:
- All 4 modes represented equally
- All 3 depth levels in each mode
- Range from simple to complex requirements
- Critical error handling paths
- Multi-step workflow orchestration
- Total runtime: ~2-3 hours for full coverage

---

## Validation Checklist

Use this checklist to verify each scenario after testing:

### Per-Scenario Validation

- [ ] Scenario ran without crashing
- [ ] Output matches expected structure
- [ ] Token usage within budget (±20%)
- [ ] Duration within estimated range (±30%)
- [ ] Framework detection accurate
- [ ] Depth level respected (QUICK outputs are shorter, DEEP more detailed)
- [ ] All required sections present
- [ ] No PII or secrets in output
- [ ] Error messages clear and actionable
- [ ] Resume capability works (where applicable)

### Cross-Scenario Validation

- [ ] Consistent output formatting across modes
- [ ] Consistent depth behavior across all modes
- [ ] Audience-specific adaptations working
- [ ] Mode routing decisions correct
- [ ] Mixed-intent workflows chain correctly
- [ ] Edge case handling doesn't crash system
- [ ] Error handling provides recovery path

### Enterprise Validation

- [ ] All scenarios complete within SLA
- [ ] No memory leaks across extended runs
- [ ] Concurrent execution doesn't interfere
- [ ] Session state persists correctly for resumption
- [ ] Large codebase handling (EC-4) respects limits
- [ ] Security: no PII leakage
- [ ] Documentation accurate and complete

---

## Metrics to Track

For each scenario, record:

1. **Execution Time:** Actual duration (target ±30% of estimate)
2. **Token Usage:** Actual vs. budgeted
3. **Success Rate:** Did it complete successfully?
4. **Quality Score:** 1-5 based on output relevance and accuracy
5. **Depth Fidelity:** Did depth level actually affect output appropriately?
6. **Framework Detection Accuracy:** Correct primary framework/language?
7. **Output Structure Compliance:** All expected sections present?
8. **Error Recovery:** If error occurred, did recovery work?

---

## Known Limitations & Caveats

1. **Token Budget Variability:** Token usage depends on actual codebase size and complexity; estimates are ±20%
2. **Framework Detection Confidence:** Some frameworks may have <90% confidence; system gracefully degrades
3. **Depth Mode Trade-off:** QUICK mode intentionally trades detail for speed; some patterns may be missed
4. **Monorepo Complexity:** Monorepos with >20 packages may exceed token budgets; system returns Tier 1 only
5. **Legacy Code Analysis:** Frameworks not in detection list will be marked as "custom" with analysis still provided
6. **Performance Profiling:** Performance analysis is static code analysis, not runtime profiling

---

## Future Extensions

- Real test execution framework integration
- Automated test harness with sample codebases
- Performance benchmarking suite
- CI/CD integration for continuous validation
- Metrics dashboard
- Customer scenario library (anonymized real-world cases)

---

**Next Steps:** Review each scenario file (01-implementation-planning.md through 04-optimization-mode.md) for detailed test specifications and implementation guidance.
