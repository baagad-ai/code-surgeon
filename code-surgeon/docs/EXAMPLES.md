# Real-World Examples

## Example 1: Adding Authentication

### Scenario
You need to add JWT authentication to a Node.js/Express API that currently has no auth.

### Command
```bash
/code-surgeon "Add JWT authentication to user API endpoints. Current system has no auth. Need to:
1. Create JWT token generation
2. Add middleware for token validation
3. Protect existing endpoints
4. Add login/logout endpoints"
```

### What You Get

**PLAN.md includes:**
- Summary: "Implement stateless JWT-based authentication with RS256 signing"
- Research: "Express app has 5 endpoints, currently unprotected. No user management system"
- Design Choices:
  - JWT over sessions (stateless, scalable)
  - RS256 signing (more secure than HS256)
  - Redis for token blacklist (optional enhancement)
- Phases:
  - Phase 1: Core JWT infrastructure
  - Phase 2: Middleware and endpoint protection
  - Phase 3: User login/logout flows
- 8 granular tasks with dependencies
- Breaking Changes: "Existing unauthenticated calls will now fail - clients must obtain JWT token"

**Surgical Prompts for each task:**
- Task 1.1: "Create JWTService class"
- Task 1.2: "Add token generation"
- Task 2.1: "Create auth middleware"
- Task 2.2: "Protect endpoints"
- etc.

---

## Example 2: Complex Bug Fix

### Scenario
A critical bug: pagination breaks with filters. Users see wrong results.

### Command
```bash
/code-surgeon "https://github.com/myorg/ecommerce/issues/567" --depth=DEEP
```

### What You Get

**Detailed root cause analysis:**
- Bug happens when: filters applied + pagination > 1
- Root cause: Offset calculation happens BEFORE filter application
- Affected files: 3 (Product model, filter service, pagination handler)
- Why it happened: Pagination logic added before filtering was refactored

**Breaking Change Analysis:**
- Behavior: Results will change (now correct, were wrong before)
- Impact: Users might see different products (this is correct)
- Migration: No code changes needed for clients, just data display changes

**Surgical Prompts with line numbers:**
```
Task 1.1: Fix pagination offset calculation
File: src/services/filterService.ts (lines 45-62)
Current: offset = (page - 1) * pageSize
Correct: Apply filters FIRST, then calculate offset
Example: Before fix, 100 total items filtered to 20, but offset assumed 100
```

---

## Example 3: Refactoring Database Layer

### Scenario
Legacy MongoDB code needs to move to PostgreSQL with TypeORM.

### Command
```bash
/code-surgeon "Refactor database layer from MongoDB to PostgreSQL with TypeORM.
Current: Mongoose models, custom queries
Target: TypeORM entities, query builder
Scope: 15 models, 200+ queries
Constraints: Zero downtime migration strategy" --depth=DEEP
```

### What You Get

**Comprehensive refactoring plan:**
- Architecture: "Two-phase migration: parallel systems → cutover"
- Design Choices:
  - TypeORM over other ORMs (mature, good typing)
  - Feature flags for gradual rollout
  - 1-week parallel run before full cutover
- Phases:
  - Phase 1: TypeORM setup and entity definitions
  - Phase 2: Query builder migration
  - Phase 3: Data migration scripts
  - Phase 4: Testing and cutover
  - Phase 5: Rollback procedures
- 12 tasks with careful ordering
- Breaking Changes:
  - Query syntax changes (internal, no API impact)
  - Performance changes (expected improvements)
  - Connection string changes (deployment impact)

**Surgical Prompts for each entity:**
- Task 1.1: "Create User TypeORM entity"
- Task 1.2: "Create Product TypeORM entity"
- Task 2.1: "Migrate findById queries"
- Task 3.1: "Create data migration script"
- etc.

---

## Example 4: React Performance Optimization

### Scenario
React app slow on mobile. Need to profile, identify bottlenecks, and optimize.

### Command
```bash
/code-surgeon "Optimize React performance on mobile devices.
Symptoms: 3-5 second load time, janky scrolling
Current: 45 components, state in Redux, no code-splitting
Target: <2 second load, smooth 60fps scrolling" --depth=STANDARD
```

### What You Get

**Performance optimization plan:**
- Research: "Identified N+1 component renders, large bundle size"
- Design Choices:
  - Code-splitting by route
  - Memo() for expensive components
  - Virtual scrolling for lists
  - Redux selector memoization
- Phases:
  - Phase 1: Measurement and profiling
  - Phase 2: Code-splitting
  - Phase 3: Component optimization
  - Phase 4: Testing and verification
- 6 tasks
- Breaking Changes: None (internal optimization)

**Surgical Prompts:**
- "Wrap ProductList with React.memo()"
- "Add code-splitting to routes"
- "Optimize Redux selectors with reselect"
- etc.

---

## Example 5: Team Workflow Example

### Scenario
Your team uses code-surgeon for all multi-file changes.

### Workflow

**Monday 9am: Product Manager creates GitHub issue**
```
Issue #234: "Add dark mode theme"
Description: [requirements]
```

**Monday 10am: Developer runs code-surgeon**
```bash
/code-surgeon "https://github.com/myorg/app/issues/234" --depth=STANDARD
```

**Monday 10:20am: Review PLAN.md**
- Team lead reviews the generated plan
- Discusses design choices
- Approves the approach

**Monday 11am: Execution starts**
Developer copies Task 1.1 surgical prompt to Claude:
```
OBJECTIVE: Create dark mode theme configuration
CONTEXT: Current app uses light theme globally via Tailwind
SCOPE: Create new theme file, integrate with Tailwind
...
[Full 9-section prompt]
```

Claude generates code for Task 1.1

**Monday 11:30am: Task 1.1 done**
Developer moves to Task 1.2 (add theme switcher component)

**Monday 4pm: All tasks complete**
Open PR with all changes, reference the code-surgeon PLAN.md

**PR review mentions:** "Follows implementation plan from code-surgeon #234"

---

## Key Takeaways

✅ **code-surgeon saves 30-40 minutes of manual planning per issue**
✅ **Generates consistent, quality implementation plans**
✅ **Surgical prompts are ready for AI agents (Claude, Cursor, etc.)**
✅ **Breaking changes are proactively identified**
✅ **Team guidelines are automatically enforced**
✅ **Complex multi-file changes become structured workflows**

See [USAGE.md](USAGE.md) for command reference and [ARCHITECTURE.md](ARCHITECTURE.md) for technical details.
