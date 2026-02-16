# Review Mode Test Scenarios

**Mode:** Review Mode (R)
**File:** 03-review-mode.md
**Total Scenarios:** 24 (8 per depth level)
**Purpose:** Test change impact assessment, breaking change detection, and safety validation across QUICK, STANDARD, and DEEP depths

---

## QUICK Depth (5 minutes, ~30K tokens)

### R-1.1: Patch Update - React Version Upgrade

**Scenario ID:** R-1.1
**Depth Level:** QUICK
**Type:** Dependency Update / Breaking Change Detection
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
A React SPA currently running React 17.0.2 wants to upgrade to React 18.0.0. Quick assessment of breaking changes and migration steps. ~150 files, standard structure.

**Requirement:**
"Assess impact of upgrading React from 17 to 18: breaking changes and migration steps"

**Input Contract:**
```json
{
  "requirement": "Upgrade React from 17 to 18",
  "mode": "review",
  "depth": "QUICK",
  "repo_root": "/test-repos/react-spa",
  "change_type": "dependency-upgrade"
}
```

**Expected Output Structure:**
- Executive Summary (1-2 sentences)
- Breaking Changes List (3-5 key changes)
  - Automatic batching (behavior change)
  - Concurrent rendering (performance improvement, no code changes needed)
  - Suspension behavior (key change)
  - etc.
- Affected Areas (2-3 sections)
  - Components using Suspense
  - Custom hooks
  - Server-side rendering (if applicable)
- Migration Steps (3-5 key steps)
  1. Update package.json
  2. Check Suspense boundaries
  3. Test custom hooks
  4. Run test suite
  5. Deploy with monitoring
- Risk Level: Low to Medium
- Estimated Effort: 2-4 hours

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Breaking changes correctly identified
- [ ] React 18-specific changes mentioned (automatic batching, etc.)
- [ ] Affected components identified
- [ ] Migration steps are clear
- [ ] Estimated effort realistic
- [ ] Output is concise (QUICK = brief)
- [ ] Risk assessment accurate
- [ ] No unnecessary detail

---

### R-1.2: Patch Update - Lodash Upgrade

**Scenario ID:** R-1.2
**Depth Level:** QUICK
**Type:** Dependency Update / Library Upgrade
**Time Budget:** 5 minutes
**Token Budget:** 30K

**Context:**
Lodash 4.17.15 → 4.17.21 (patch with security fixes). Application has ~100 files. Need quick assessment of impact and safety.

**Requirement:**
"Assess impact of upgrading Lodash from 4.17.15 to 4.17.21"

**Input Contract:**
```json
{
  "requirement": "Upgrade Lodash from 4.17.15 to 4.17.21",
  "mode": "review",
  "depth": "QUICK",
  "repo_root": "/test-repos/node-app",
  "change_type": "dependency-upgrade"
}
```

**Expected Output Structure:**
- Executive Summary (security fixes, likely safe)
- Breaking Changes: None (patch release)
- Affected Areas
  - Lodash functions used in app
  - Dependency chain
- Security Fixes (1-2 key fixes)
  - Vulnerability summary
- Migration Steps
  1. Update package.json
  2. Run tests
  3. Deploy
- Risk Level: Very Low
- Estimated Effort: 30 minutes

**Token Usage Expected:** 25-35K
**Accuracy Expected:** 85%

**Validation Criteria:**
- [ ] Correctly identifies as patch (no breaking changes)
- [ ] Security fixes mentioned
- [ ] Dependency impact assessed
- [ ] Steps are simple and straightforward
- [ ] Risk assessment low (patch)
- [ ] Recommended to apply upgrade

---

## STANDARD Depth (15 minutes, ~60K tokens)

### R-2.1: Minor Change - Add Email Notifications

**Scenario ID:** R-2.1
**Depth Level:** STANDARD
**Type:** Feature Addition / Business Logic Change
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Django + React SaaS wants to add email notifications when users update their profiles. ~300 files, moderate complexity, established patterns.

**Requirement:**
"Add email notifications when user updates profile - assess impact on existing system"

**Input Contract:**
```json
{
  "requirement": "Add email notification when user profile is updated",
  "mode": "review",
  "depth": "STANDARD",
  "repo_root": "/test-repos/django-saas",
  "change_type": "feature"
}
```

**Expected Output Structure:**
- Executive Summary
  - Impact scope (medium)
  - Key areas affected (models, signals, API)
  - Risk level (low to medium)
- Impact Analysis
  - User model changes (add email notification preferences)
  - Database changes (new column or settings table)
  - API changes (update user endpoint effects)
  - Frontend changes (notification settings UI)
- Affected Modules (4-6 modules)
  - User model and management
  - API endpoints (PUT /users/:id)
  - Frontend forms
  - Email service integration
  - Background tasks (celery, if applicable)
  - Tests (need updates)
- Breaking Changes Analysis
  - User API might change (return updated_at or new field?)
  - Database migration required
  - No API contract breaking (additive only)
- Dependencies & Risks
  - Email service reliability (what if email service down?)
  - Notification preference storage
  - Rate limiting on emails
  - Unsubscribe mechanism
- Data Migration Requirements
  - Need to backfill notification preferences
  - Estimated data volume
  - Migration strategy
- Testing Impact
  - New tests needed (email sending)
  - Existing tests may need updates
  - Mock email service
  - Integration test scenarios
- Performance Implications
  - Added database queries (check preferences)
  - Email service latency (async recommended)
  - Impact on profile update response time (should be minimal with async)
- Rollout Strategy
  - Feature flag for gradual rollout
  - Can disable email sending if issues arise
  - Monitoring recommendations
- Validation Checklist
  - [ ] Notification preferences persisted
  - [ ] Emails send asynchronously
  - [ ] Unsubscribe mechanism works
  - [ ] Tests passing
  - [ ] No PII in email logs
  - [ ] Rate limiting prevents spam

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] All affected modules identified (≥4 modules: models, API, frontend, services)
- [ ] Breaking changes clearly assessed (additive only, no breaking changes)
- [ ] Database changes outlined (new columns/tables and migrations)
- [ ] API changes documented (fields added to endpoints)
- [ ] Testing needs clear (≥5 new test scenarios identified)
- [ ] Risk assessment realistic with specific mitigation (low to medium)
- [ ] Rollout strategy safe (feature flag + async implementation)
- [ ] Performance considerations included (query/latency impact estimated)
- [ ] Data migration strategy provided (backfill approach for existing users)

---

### R-2.2: API Change - Rename Endpoint

**Scenario ID:** R-2.2
**Depth Level:** STANDARD
**Type:** Breaking API Change / Versioning
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
An Express API has endpoint `/api/users` and wants to rename it to `/api/v2/users` for versioning. The endpoint is used by multiple clients (web, mobile, third-party integrations). ~200 files.

**Requirement:**
"Rename API endpoint from /api/users to /api/v2/users - assess impact and breaking changes"

**Input Contract:**
```json
{
  "requirement": "Rename endpoint /api/users to /api/v2/users for API versioning",
  "mode": "review",
  "depth": "STANDARD",
  "repo_root": "/test-repos/express-api",
  "change_type": "breaking-change"
}
```

**Expected Output Structure:**
- Executive Summary
  - BREAKING CHANGE: All clients must update
  - Impact scope (major)
  - All endpoint versions used by clients must be identified
  - Risk level (high without migration strategy)
- Breaking Changes Identified
  - `/api/users` endpoints no longer available
  - Mobile app will break
  - Third-party integrations will break
  - Web client will break
- Client Inventory (action required)
  - List all clients using this endpoint
  - Internal web app (can update in same release)
  - Mobile app (slower release cycle)
  - Third-party integrations (no control)
- Migration Strategy (recommended)
  - Option 1: Deprecation period (run both old and new endpoints for 6-12 months)
  - Option 2: Dual versioning (new endpoint, old endpoint deprecated but still works)
  - Option 3: Force migration (all clients must update, set cutoff date)
- Implementation Details
  - Keep `/api/users` → redirect to `/api/v2/users`
  - Or: Both endpoints work, old endpoint marked as deprecated in response header
  - Documentation update
  - Changelog/release notes
- Affected Modules (3-4 modules)
  - Route definitions
  - API documentation
  - Test suite
  - Client libraries (if any)
- Testing Impact
  - All endpoints need testing for both versions
  - Backward compatibility testing
  - Client integration tests
- Client Communication Plan
  - When will old endpoint be removed?
  - Documentation/migration guide for third parties
  - Support plan for clients updating
- Timeline
  - Deprecation notice: Now
  - Dual support period: 6-12 months
  - Old endpoint removal: (date)
- Monitoring & Validation
  - Monitor usage of old endpoint
  - Track client migration
  - Alert on new usage of old endpoint (after migration)

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Breaking change clearly identified
- [ ] All client types identified (web, mobile, third-party)
- [ ] Migration strategy presented
- [ ] Deprecation plan included
- [ ] Timeline realistic
- [ ] Communication plan outlined
- [ ] Testing impact clear
- [ ] Monitoring approach provided
- [ ] Both old and new endpoints addressed

---

### R-2.3: Major Change - MongoDB to PostgreSQL Migration

**Scenario ID:** R-2.3
**Depth Level:** STANDARD
**Type:** Database Migration / Architectural Change
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
A Node.js application (Mongoose + MongoDB) wants to migrate to PostgreSQL (Knex or TypeORM). ~250 files, ~5 years old, complex data model.

**Requirement:**
"Assess impact of migrating from MongoDB to PostgreSQL: schema changes, data migration, and risks"

**Input Contract:**
```json
{
  "requirement": "Migrate database from MongoDB to PostgreSQL",
  "mode": "review",
  "depth": "STANDARD",
  "repo_root": "/test-repos/node-mongo-app",
  "change_type": "database-migration"
}
```

**Expected Output Structure:**
- Executive Summary
  - Impact scope (MASSIVE - affects entire data layer)
  - Risk level (CRITICAL - potential data loss)
  - Estimated effort: 4-8 weeks
  - Recommendation: Phased approach with parallel systems
- Schema Transformation Analysis
  - Collections → Tables mapping
  - Nested documents → normalized schema
  - Array fields → junction tables
  - Data type transformations (ObjectId → UUID, Date handling)
  - Index strategy (MongoDB indexes → PostgreSQL indexes)
- Affected Modules (all data layers)
  - Data access layer (Mongoose → ORM)
  - All models and schemas
  - All queries and aggregations
  - All indexes
  - All transactions (MongoDB → PostgreSQL consistency)
- Breaking Changes
  - Query API completely different
  - Transaction handling changes
  - Error handling changes (MongoDB-specific errors gone)
  - Performance characteristics different
  - Connection pooling required
- Data Consistency Concerns
  - ACID transactions available (better than MongoDB)
  - Foreign key constraints needed
  - Referential integrity enforcement
  - Data validation in DB layer
- Data Migration Strategy
  - Data volume (how much data?)
  - Downtime requirements (zero-downtime desired?)
  - Verification approach (sample validation, full validation)
  - Rollback plan (keep MongoDB backup)
  - Parallel systems (run both during transition)
- ORM/Driver Changes
  - Mongoose → (TypeORM, Knex, Sequelize, or raw SQL)
  - Connection pooling setup
  - Transaction handling
  - Error handling refactoring
  - Query building differences
- Performance Implications
  - MongoDB strengths lost (flexible schema, fast writes)
  - PostgreSQL advantages (ACID, complex queries, JOINs)
  - Indexing strategy needed
  - Query optimization needed
  - May need caching strategy (Redis)
- Testing Impact
  - All data layer tests need rewrite
  - Query tests now different
  - Transaction tests differ
  - Integration test data setup changes
  - E2E testing with PostgreSQL
- Deployment & Rollout
  - Rollout in phases (1-2 feature areas at a time)
  - Feature flags for new code path
  - Parallel writes (write to both DBs during transition)
  - Read from new DB, fallback to old
  - Cutover process
- Monitoring & Validation
  - Data consistency checks
  - Query performance monitoring
  - Error rate monitoring
  - Fallback mechanism monitoring
  - Performance regression detection

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Schema transformation strategy clear
- [ ] All modules affected identified
- [ ] Data migration approach provided
- [ ] Downtime strategy addressed
- [ ] Rollback plan included
- [ ] ORM/driver migration outlined
- [ ] Performance implications discussed
- [ ] Testing impact clear
- [ ] Phased rollout strategy provided
- [ ] Risk level (CRITICAL) clearly communicated

---

### R-2.4: Major Change - JWT to OAuth2 Authentication

**Scenario ID:** R-2.4
**Depth Level:** STANDARD
**Type:** Authentication System Change / Breaking Change
**Time Budget:** 15 minutes
**Token Budget:** 60K

**Context:**
An Express + React application currently uses JWT authentication and wants to migrate to OAuth2 (for third-party integrations). ~200 files, existing JWT implementation with refresh tokens.

**Requirement:**
"Migrate authentication from JWT to OAuth2 - assess impact on all components"

**Input Contract:**
```json
{
  "requirement": "Migrate authentication system from JWT to OAuth2",
  "mode": "review",
  "depth": "STANDARD",
  "repo_root": "/test-repos/express-jwt-app",
  "change_type": "authentication-change"
}
```

**Expected Output Structure:**
- Executive Summary
  - Impact scope (MAJOR - affects auth throughout system)
  - Risk level (HIGH - auth is critical)
  - Estimated effort: 2-3 weeks
  - Breaks all clients (need simultaneous update)
- Architecture Comparison
  - Current: JWT tokens (stateless, expire after time)
  - New: OAuth2 with refresh tokens (more secure, stateful)
  - Authorization server needed (local or third-party)
  - Token endpoint, authorization endpoint, resource endpoint
- Affected Components (all auth-related)
  - Login/logout endpoints
  - Middleware (verify JWT → verify OAuth token)
  - Token generation → token exchange via OAuth server
  - Refresh token handling
  - Session management
  - API endpoints (extract user from token)
  - Frontend authentication flow
  - Mobile app (if applicable)
  - Third-party integrations (if any)
- Breaking Changes (ALL clients must update)
  - Token format completely different
  - Login flow changed (authorization code flow vs direct token)
  - Token endpoint changed
  - Refresh token mechanism changed
  - Headers might change (Bearer token format should be same)
  - Error responses differ
- Deployment of OAuth Server
  - If using third-party (Auth0, Okta): integration work
  - If self-hosted: new service to deploy and maintain
  - Key management
  - Certificate/secret rotation
  - High availability setup
- Client Migration
  - Web client: update login flow
  - Mobile app: update authentication library
  - Third-party integrations: provide new OAuth2 credentials
  - Backward compatibility period (both JWT and OAuth2? Not recommended for auth)
- Data Migration
  - User sessions (invalidate all existing JWT tokens)
  - Refresh tokens (migrate or invalidate)
  - OAuth client credentials provisioning
  - API key migration (if using for service accounts)
- Risk Assessment
  - User lockout risk (all users need to re-login)
  - Third-party integration breaking
  - Security impact (OAuth2 more secure if implemented correctly)
  - Complexity increase
  - Operational overhead
- Security Improvements
  - OAuth2 framework more mature
  - Token scope support (fine-grained permissions)
  - Refresh token rotation
  - Authorization server separation
  - Better compliance (OIDC if used)
- Testing Impact
  - Auth tests require OAuth2 mocking
  - Token verification tests differ
  - Integration tests with OAuth server
  - E2E tests with full OAuth flow
  - Client tests for new auth flow
- Rollout Strategy
  - Phased approach difficult (auth is binary)
  - Option: Parallel systems (both auth methods) - not ideal
  - Option: Coordinated cutover (all clients updated simultaneously)
  - Option: Feature flag with both systems
- Monitoring & Validation
  - Login success rate monitoring
  - OAuth token validation rate
  - Token refresh frequency
  - Auth-related error rates
  - Third-party integration status

**Token Usage Expected:** 55-65K
**Accuracy Expected:** 95%

**Validation Criteria:**
- [ ] Architecture comparison clear
- [ ] All affected components identified
- [ ] Breaking changes explicitly listed
- [ ] Client migration plan outlined
- [ ] OAuth server deployment addressed
- [ ] Security improvements highlighted
- [ ] Risk level (HIGH) clearly communicated
- [ ] Testing strategy provided
- [ ] Rollout challenges acknowledged
- [ ] Timeline realistic

---

## DEEP Depth (30 minutes, ~90K tokens)

### R-3.1: Critical Change - Payment Processing Refactor (PCI Impact)

**Scenario ID:** R-3.1
**Depth Level:** DEEP
**Type:** Critical System Change / Compliance Impact
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A fintech SaaS (Node.js + React) currently processes payments directly and wants to move to a payment processor (Stripe) for PCI-DSS compliance. ~400 files, handles credit cards, regulatory oversight.

**Requirement:**
"Refactor payment processing to use Stripe API instead of direct processing - comprehensive safety and compliance assessment"

**Input Contract:**
```json
{
  "requirement": "Refactor payment processing to move to Stripe instead of direct card processing",
  "mode": "review",
  "depth": "DEEP",
  "repo_root": "/test-repos/fintech-saas",
  "change_type": "critical-compliance",
  "compliance": "PCI-DSS"
}
```

**Expected Output Structure:**
- Executive Summary
  - Impact scope (MASSIVE - affects revenue-generating system)
  - Risk level (CRITICAL - payment processing)
  - Compliance impact (PCI-DSS improvement)
  - Estimated effort: 3-6 weeks
  - Timeline: Phased with parallel systems
- Compliance & Regulatory Assessment
  - Current compliance status (likely PCI-DSS scope is large)
  - Future compliance status (PCI-DSS scope reduced significantly)
  - Stripe compliance certifications
  - Liability shifts (to Stripe, away from company)
  - Audit implications
  - Regulatory reporting changes
- Architecture Analysis
  - Current architecture (payment processing in-house)
  - New architecture (Stripe as external service)
  - Token management (Stripe tokens vs stored cards)
  - Data flow changes
  - Webhook handling (Stripe events)
- Affected Modules (pervasive)
  - Payment processing API
  - Billing module
  - Invoice generation
  - Refund processing
  - Subscription management
  - Webhook receivers
  - Fraud detection
  - Customer payment methods storage
  - Database schema (payment records)
  - Frontend checkout
  - Mobile app payments
  - API integrations
- Breaking Changes (comprehensive)
  - Payment API endpoints change
  - Payment processing flow changes
  - Webhook event format changes
  - Refund process changes
  - Dispute handling changes
  - Subscription management changes
  - Error handling changes
  - API response structures change
- Data Migration Strategy
  - Customer payment methods (no card details stored - delete safely)
  - Payment history (archive, no need to migrate)
  - Subscription data (validate mapping to Stripe subscriptions)
  - Invoice data (maintain local records or pull from Stripe)
  - Reconciliation data
  - Audit trail requirements
- Stripe Integration Details
  - API keys management (test vs prod)
  - Webhook endpoint setup
  - Payment method tokenization
  - Payment intent vs charges (intent recommended)
  - Webhook signature verification
  - Error handling (network failures, retries)
  - Rate limiting
  - Account reconciliation
- Risk Assessment (CRITICAL)
  - Revenue interruption risk (if migration fails)
  - Data inconsistency risk (Stripe system of truth vs local DB)
  - Refund handling complexity (Stripe vs local accounting)
  - Webhook failure handling (payment completed but webhook missed?)
  - Timing risk (change over weekend for testing)
  - Security risk (reduced but different - Stripe dependency)
  - Operational risk (new service dependency)
  - Cost change (Stripe fees vs internal processing costs)
- Security Implications
  - Positive: No card data in internal systems (PCI scope reduction)
  - Positive: Stripe handles tokenization and compliance
  - Negative: Dependency on external service
  - Negative: Webhook endpoint must be secure (verify signatures)
  - Negative: API keys must be protected
  - Negative: Stripe API compromise could affect system
- Testing Strategy
  - Stripe test API key usage
  - Full payment flow testing (test cards)
  - Webhook testing (verify signatures, replay attacks)
  - Error scenario testing (payment failures, retries)
  - Refund flow testing
  - Subscription testing
  - Integration testing with accounting system
  - E2E testing with real customers (staged rollout)
  - Concurrency testing (simultaneous payments)
  - Performance testing (Stripe API latency)
  - Disaster recovery testing (Stripe API down, local system recovery)
- Rollout Strategy (phased with parallel systems)
  - Phase 1: Parallel processing (process with both systems)
    - Accept payments via Stripe (new)
    - Keep legacy system active (old)
    - Compare results, reconcile
    - Duration: 1-2 weeks
  - Phase 2: Cutover
    - Route all new payments to Stripe
    - Keep legacy system for historical data
    - Customer support readiness
    - Duration: Cutover event
  - Phase 3: Legacy shutdown
    - Archive legacy system
    - Data retention per compliance
  - Rollback plan (if Stripe integration fails)
    - Within first 24 hours: flip back to legacy
    - Process any Stripe payments manually
    - Full restoration within 4 hours
- Monitoring & Validation
  - Payment success rate monitoring
  - Webhook delivery monitoring
  - Error rate monitoring
  - Stripe balance reconciliation (daily)
  - Revenue tracking
  - Customer complaint rate monitoring
  - Fraud detection metrics
  - Latency monitoring
  - SLA compliance (Stripe uptime)
- Operational Changes
  - Support process changes (how to handle disputes?)
  - Refund process (Stripe dashboard vs automated)
  - Payment troubleshooting (Stripe vs local)
  - Accounting integration (manual reconciliation?)
  - Automation (sync payments to accounting system)
  - Documentation updates
  - Team training
- Communication Plan
  - Customer notification (transparent about improvements)
  - Support team training
  - Internal stakeholder alignment (finance, compliance, ops)
  - Third-party integrations that depend on payment data
- Cost Analysis
  - Stripe fees vs current processing costs
  - Development effort cost
  - Operational overhead changes
  - Compliance/audit cost savings
  - Risk reduction value

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Compliance impact (PCI-DSS) clearly assessed
- [ ] All affected modules identified
- [ ] Breaking changes comprehensively listed
- [ ] Data migration strategy detailed
- [ ] Stripe API integration approach clear
- [ ] Risk assessment addresses payment interruption
- [ ] Security implications (positive and negative) documented
- [ ] Testing strategy is exhaustive
- [ ] Phased rollout strategy with parallel systems
- [ ] Rollback plan provided
- [ ] Monitoring approach comprehensive
- [ ] Communication plan outlined
- [ ] Cost analysis included
- [ ] Timeline realistic for critical system change

---

### R-3.2: Critical Change - Core Authentication System Refactoring

**Scenario ID:** R-3.2
**Depth Level:** DEEP
**Type:** Critical System Change / Security Impact
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A mature SaaS platform (Django backend, React/Native frontends) has security vulnerabilities in its authentication system and needs comprehensive refactoring. ~600 files, multiple client types (web, mobile), API-first. Security audit revealed issues.

**Requirement:**
"Refactor core authentication system to fix security vulnerabilities and improve architecture"

**Input Contract:**
```json
{
  "requirement": "Refactor core authentication system to address security vulnerabilities",
  "mode": "review",
  "depth": "DEEP",
  "repo_root": "/test-repos/django-saas-auth",
  "change_type": "critical-security",
  "severity": "critical"
}
```

**Expected Output Structure:**
- Executive Summary
  - Vulnerability scope (CRITICAL - authentication is foundational)
  - Risk level (CRITICAL - all users affected)
  - Estimated effort: 4-8 weeks
  - Timeline: Phased with feature flags
  - Security recommendation: Prioritize this work
- Current Authentication Analysis
  - Current mechanism (sessions, JWT, other?)
  - Identified vulnerabilities
    - Session fixation risks
    - Token validation gaps
    - Privilege escalation vectors
    - Password handling issues
    - MFA gaps (if present)
    - Logout completeness
    - CSRF protection
    - XSS in auth code
  - Compliance status (SOC 2, GDPR, HIPAA impacts)
- Proposed Architecture
  - New authentication mechanism
  - Threat model addressed
  - Security improvements (list each vulnerability fix)
  - Backward compatibility (phased migration)
  - Token mechanism (JWT, OAuth2, hybrid)
  - MFA support (mandatory)
- Affected Components (extensive)
  - Authentication middleware
  - Login endpoint
  - Logout endpoint
  - Token refresh mechanism
  - Session management (if applicable)
  - API authentication
  - Web client authentication
  - Mobile app authentication
  - Service-to-service authentication (if any)
  - Permission/authorization system
  - User model
  - Password reset flow
  - Email verification flow
  - Two-factor authentication
  - Audit logging
  - Admin authentication
  - API key authentication (if used)
  - OAuth/social login (if used)
- Breaking Changes (all clients affected)
  - Authentication flow changes
  - Token format changes
  - API response changes
  - Error handling changes
  - Session behavior changes
  - MFA requirement changes
  - Device tracking (new or changed)
  - Logout behavior changes
- Data Migration
  - Session invalidation (force re-login)
  - User credentials review
  - Password reset campaign (if hashing algorithm changed)
  - Device tokens (invalidate existing)
  - API keys (invalidate and re-issue)
  - MFA enrollment (how to handle existing users without MFA)
  - Audit trail integrity
- Migration Strategy (phased with feature flags)
  - Phase 1: Preparation (1-2 weeks)
    - New auth system development
    - Extensive testing
    - Deploy behind feature flag
    - Internal testing only
  - Phase 2: Parallel operation (2-3 weeks)
    - Enable for % of users (gradual rollout)
    - Monitor extensively
    - Compare auth success rates
    - Performance testing
    - Security validation
  - Phase 3: Cutover (1-2 weeks)
    - Increase percentage daily
    - Support team on standby
    - Rollback ready
  - Phase 4: Legacy removal (1 week)
    - Remove old auth code
    - Cleanup
- Client Migration Requirements
  - Web client updates
  - Mobile app updates (iOS and Android)
  - Third-party API integrations (if external clients)
  - CLI tools (if any)
  - Service-to-service auth updates
- Testing Strategy (comprehensive for security)
  - Unit tests for auth modules
  - Integration tests (full auth flows)
  - Security testing:
    - Privilege escalation attempts
    - Token tampering
    - Session hijacking
    - CSRF attacks
    - XSS attacks
    - Timing attacks
    - Brute force resistance
    - Rate limiting
  - Penetration testing (hire professionals)
  - Load testing (auth under high traffic)
  - Concurrency testing (simultaneous logins)
  - E2E testing (web, mobile)
  - Rollback scenario testing
- Risk Assessment & Mitigation
  - User lockout risk (if migration fails, users can't login)
    - Mitigation: Feature flag for instant rollback
  - Token expiration risk (old tokens invalidated)
    - Mitigation: Graceful token refresh before cutover
  - Third-party integration breaking
    - Mitigation: Communication plan, migration guide
  - Session loss risk
    - Mitigation: Clear messaging to users
  - Security regression risk (new code has vulnerabilities)
    - Mitigation: Extensive security testing, penetration test
  - Compliance violation risk (during transition)
    - Mitigation: Compliance team involvement
- Security Hardening Details
  - Password hashing (bcrypt, argon2, scrypt)
  - Token signing (HS256, RS256)
  - Token expiration (access token, refresh token)
  - Rate limiting (prevent brute force)
  - Secure transmission (HTTPS, secure cookies)
  - Same-site cookie policy
  - HSTS headers
  - CSP headers (prevent XSS)
  - Logout completeness (invalidate all sessions/tokens)
  - MFA implementation details
  - Device fingerprinting (optional)
  - Anomalous login detection
  - Audit trail requirements
- Operational Considerations
  - Support team training
  - Troubleshooting guide (common issues)
  - Monitoring setup (auth error rates, success rates)
  - Alerting (unusual patterns, failures)
  - Logging (audit trail, secure)
  - Key rotation process
  - Incident response plan
  - User communication strategy
- Documentation Updates
  - Architecture documentation
  - API documentation
  - Client integration guides
  - Security documentation
  - Troubleshooting guide
  - Compliance documentation
- Cost & Timeline
  - Development effort
  - Testing effort (security is complex)
  - Deployment preparation
  - Training
  - Ongoing operational cost
  - Timeline: 4-8 weeks realistic
- Alternatives Considered
  - Third-party auth (Auth0, Okta) - discussed, trade-offs
  - Minimal changes vs full refactor - security argued for full refactor

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Vulnerabilities clearly identified
- [ ] New architecture explicitly documented
- [ ] All affected components listed
- [ ] Breaking changes detailed
- [ ] Migration strategy is phased with feature flags
- [ ] All client types (web, mobile, API) addressed
- [ ] Testing strategy is security-focused (penetration test included)
- [ ] Risk assessment addresses user lockout
- [ ] Rollback plan clear
- [ ] Monitoring for security issues planned
- [ ] Compliance implications addressed
- [ ] Timeline realistic (4-8 weeks)
- [ ] Communication plan outlined

---

### R-3.3: Critical Change - Database Schema Reorganization (Data Integrity)

**Scenario ID:** R-3.3
**Depth Level:** DEEP
**Type:** Critical Change / Data Integrity Impact
**Time Budget:** 30 minutes
**Token Budget:** 90K

**Context:**
A PostgreSQL database has grown organically over 8 years with ~200 tables, poor normalization, N+1 queries common, and performance degrading. A comprehensive reorganization is proposed: denormalization for performance, proper indexing, constraint enforcement.

**Requirement:**
"Reorganize database schema for performance, normalization, and data integrity - comprehensive assessment"

**Input Contract:**
```json
{
  "requirement": "Reorganize database schema for better performance, normalization, and integrity",
  "mode": "review",
  "depth": "DEEP",
  "repo_root": "/test-repos/postgres-saas",
  "change_type": "database-refactor",
  "severity": "critical"
}
```

**Expected Output Structure:**
- Executive Summary
  - Scope (MASSIVE - touches every data access point)
  - Risk level (CRITICAL - data integrity risk)
  - Estimated effort: 6-12 weeks
  - Timeline: Phased migration with parallel systems
  - Data backup: Extensive required
- Current Schema Analysis
  - Database statistics (table count, row count, size)
  - Normalization issues (over-normalized, under-normalized)
  - N+1 query patterns identified
  - Index analysis (missing, inefficient)
  - Constraint analysis (missing constraints, data integrity gaps)
  - Performance bottlenecks (specific slow queries)
  - Referential integrity issues (orphaned records)
  - Data quality issues (duplicates, nulls in critical fields)
- Proposed Schema
  - Normalization approach (3NF, BCNF, denormalization where needed)
  - Table restructuring (merges, splits)
  - Column changes (retyping, renaming)
  - Index strategy (new indexes, removed indexes)
  - Constraint additions (FK, unique, check)
  - Partition strategy (if table is very large)
  - Archive strategy (historical data handling)
- Affected Components (extensive)
  - All ORM models
  - All queries (likely need rewriting)
  - All database-touching code
  - All views (if using database views)
  - All stored procedures (if any)
  - All triggers (if any)
  - Reporting queries (if any)
  - API responses (might change structure)
  - Frontend data expectations (might change)
  - Caching layer (cache key changes)
- Breaking Changes (all data access affected)
  - Table names/structures changed
  - Column names/types changed
  - Query results structures changed
  - API response structures changed (possibly)
  - Relationship loading changes (no more N+1 possible)
  - Default values might change
  - Constraints might reject previously valid data
- Data Migration Strategy (complex)
  - Backup strategy (comprehensive)
    - Full backup before any changes
    - Test restore procedure
    - Backup frequency during migration
  - Zero-downtime migration approach
    - Parallel table setup (old and new schema running)
    - Dual writes (write to both schemas)
    - Validation (compare results)
    - Cutover (switch reads to new schema)
    - Remove old schema
  - Data validation strategy
    - Row count validation (old vs new)
    - Checksum validation (data integrity)
    - Sampling validation (spot check)
    - Referential integrity checks
    - Business logic validation (specific business rules)
  - Rollback procedure
    - Can we rollback after cutover?
    - Time window for rollback
    - Data consistency during rollback
  - Performance verification
    - Query performance on new schema
    - Index effectiveness
    - Cache hit rates
  - Timing (phased migration)
    - Migration of large tables in steps
    - Off-peak hours for cutover
    - Weekend for larger operations
- ORM Layer Changes
  - Model definition updates
  - Query builder updates (if using dynamic queries)
  - Relationship changes (associations might differ)
  - Default values changes
  - Validation changes
  - Migration file generation
  - Version management (old code vs new code)
- Risk Assessment & Mitigation
  - Data loss risk (extensive backups required)
    - Mitigation: Test restore, multiple backup locations
  - Data corruption risk (referential integrity)
    - Mitigation: Validation checks, test environment first
  - Downtime risk (if schema lock required)
    - Mitigation: Parallel systems, zero-downtime approach
  - Application crash risk (if code doesn't handle new schema)
    - Mitigation: Extensive testing, feature flag
  - Performance regression (if new schema not properly optimized)
    - Mitigation: Query plans analyzed, indexes verified
  - Concurrent operation issues (dual writes)
    - Mitigation: Transaction handling, conflict resolution
  - Rollback complexity (schema changes hard to rollback)
    - Mitigation: Small batch migrations, snapshot ability
- Testing Strategy (comprehensive for data)
  - Unit tests (ORM layer)
  - Integration tests (full queries)
  - Data validation tests
    - Row count matching
    - Checksum matching
    - Referential integrity
    - Business logic validation
  - Performance tests
    - Query performance baseline
    - Load testing on new schema
    - Concurrent access testing
  - Data migration tests (specific test data)
  - Parallel system tests (dual writes)
  - Rollback tests
  - E2E testing
- Deployment & Rollout
  - Phase 1: New schema setup
    - Create new tables (empty)
    - Deploy code that can write to both (if using dual write)
  - Phase 2: Data migration
    - Copy data from old to new
    - Verify data integrity
    - Performance testing
  - Phase 3: Validation
    - Run validation checks
    - A/B testing (some queries hit old, some hit new)
    - Monitor for issues
  - Phase 4: Cutover
    - Switch all reads to new schema
    - Monitor closely
    - Rollback ready for 24 hours
  - Phase 5: Cleanup
    - Archive old tables
    - Delete after retention period
- Monitoring & Alerting
  - Query performance monitoring
  - Slow query log analysis
  - Index usage monitoring
  - Lock contention monitoring
  - Data validation monitoring
  - Cache hit rate monitoring
  - Application error rate monitoring
  - Database size monitoring
- Documentation Updates
  - Schema documentation
  - Data dictionary
  - Query optimization guide
  - Migration playbook (for future maintenance)
  - Rollback procedures
  - Operational guide (indexes, constraints, maintenance)
- Alternatives Considered
  - Database sharding (vs schema reorganization)
  - Caching layer (vs schema optimization)
  - Read replicas (vs schema optimization)
  - Archive/partition strategy (vs schema only)
- Cost & Timeline
  - Development effort
  - Testing effort (data validation is complex)
  - Deployment effort
  - Operational overhead (monitoring, validation)
  - Timeline: 6-12 weeks realistic (can't be rushed with data)
  - Cost: Significant development and operations time

**Token Usage Expected:** 85-95K
**Accuracy Expected:** 99%

**Validation Criteria:**
- [ ] Current schema analysis comprehensive
- [ ] Proposed schema clearly documented
- [ ] All affected components identified
- [ ] Zero-downtime migration strategy provided
- [ ] Data migration approach detailed (with backups)
- [ ] Risk assessment addresses data loss and corruption
- [ ] Testing strategy includes data validation
- [ ] Rollback procedure clear
- [ ] Monitoring comprehensive
- [ ] Timeline realistic (6-12 weeks for critical data change)
- [ ] Alternatives evaluated
- [ ] Operations team can execute plan

---

## Validation Checklist (All Review Scenarios)

After running each scenario, verify:

### Output Structure
- [ ] Executive summary with clear risk assessment
- [ ] Breaking changes explicitly listed
- [ ] Affected modules identified
- [ ] Migration strategy provided
- [ ] Testing approach outlined

### Content Quality
- [ ] Risk level appropriate to change type
- [ ] Recommendations are safety-oriented
- [ ] Rollback plan exists for breaking changes
- [ ] Timeline estimates realistic
- [ ] All stakeholders' concerns addressed

### Depth Adherence
- [ ] QUICK: 3-4 sections, brief, risk level stated
- [ ] STANDARD: 8-10 sections, detailed, comprehensive
- [ ] DEEP: 15+ sections, exhaustive with alternatives

### Metrics
- [ ] Execution time within ±30% of estimate
- [ ] Token usage within ±20% of budget
- [ ] Framework detection accurate
- [ ] No PII or secrets in output
