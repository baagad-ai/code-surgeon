# Usage Guide

## Basic Commands

### Analyze a Requirement
```bash
/code-surgeon "Add JWT token refresh to authentication flow"
```

### Analyze a GitHub Issue
```bash
/code-surgeon "https://github.com/myorg/myrepo/issues/234"
```

### Use Specific Depth Mode
```bash
# Quick analysis (5 min, 85% accuracy)
/code-surgeon "Fix pagination bug" --depth=QUICK

# Standard analysis (15 min, 95% accuracy) - DEFAULT
/code-surgeon "Add new feature" --depth=STANDARD

# Deep analysis (30 min, 99% accuracy)
/code-surgeon "Refactor authentication system" --depth=DEEP
```

### Resume Interrupted Session
```bash
/code-surgeon-resume surgeon-20250212-abc123xyz
```

### Clear Cache
```bash
/code-surgeon-clear-cache
```

---

## Choosing Depth Mode

**Answer these 4 questions:**

### 1. Can you describe the change in one sentence?
- NO → Define it first, then return
- YES → Continue

### 2. How many files will this affect?
- 1-3 files → **QUICK mode**
- 5-8 files → **STANDARD mode** ← default
- 8+ files → **STANDARD or DEEP mode**

### 3. What's the risk if something breaks?
- Low (isolated bug fix) → **QUICK mode**
- Medium (new feature) → **STANDARD mode**
- High (security, payments, architecture) → **DEEP mode**

### 4. How much time can you invest?
- <10 minutes → **QUICK**
- 15-20 minutes → **STANDARD**
- 30+ minutes → **DEEP**

---

## Understanding Outputs

code-surgeon generates 3 files in `.claude/planning/sessions/<session-id>/`:

### PLAN.md (Human-Readable)
- Executive summary
- Research findings
- Design choices
- Implementation phases
- Granular tasks
- **Surgical prompts** (ready to copy-paste)
- Breaking changes analysis
- Verification checklist

### plan.json (Machine-Readable)
- Structured format
- Task dependencies
- Surgical prompts with metadata
- Breaking changes with impact analysis

### interactive.json (Step-Through CLI)
- Interactive mode
- Progress tracking
- Guided execution

---

## Team Guidelines

### Create .claude/team-guidelines.md

```markdown
# Team Guidelines

## Code Style
- Use camelCase for variables, PascalCase for classes
- Max line length: 100 characters
- Use const over let

## Architecture Patterns
- Use dependency injection for services
- Error handling: Always use try-catch in async
- State management: Redux for global, Context for local

## Security
- Never hardcode secrets or API keys
- Validate all user input
- Sanitize output for XSS prevention

## Framework-Specific
### React
- Use functional components with hooks
- Prop types or TypeScript for props
- Custom hooks for reusable logic

### Django
- Use Django ORM for database
- Class-based views preferred
- Middleware for cross-cutting concerns
```

code-surgeon automatically:
- Loads these guidelines
- Incorporates them into surgical prompts
- Validates generated code against them
- Flags violations in breaking changes

---

## Workflow Example

### Step 1: Run code-surgeon
```bash
/code-surgeon "Add OAuth2 authentication to user service"
```

### Step 2: Wait for analysis (15 min for STANDARD)
```
Phase 1: Analyzing requirement (2 min)
Phase 2: Researching codebase (5 min)
Phase 3: Creating implementation plan (3 min)
Phase 4: Generating surgical prompts (2 min)
Phase 5: Formatting outputs (1 min)

Analysis complete! Session: surgeon-20250212-xyz123
```

### Step 3: Review PLAN.md
```bash
cat .claude/planning/sessions/surgeon-20250212-xyz123/PLAN.md
```

### Step 4: Copy First Surgical Prompt
From PLAN.md, copy the prompt for Task 1.1:
```
## Task 1.1: Create OAuth2Service

### Objective
Create an OAuth2Service class that handles token exchange...

### Context
Current auth system uses basic JWT...

### Scope
Files to modify: src/services/auth.ts
...

[Full 9-section surgical prompt]
```

### Step 5: Hand to Your AI Agent
Paste the surgical prompt to Claude, Cursor, or your preferred AI agent.

### Step 6: Execute Remaining Tasks
After Task 1.1 completes, move to Task 1.2, following the task dependencies.

---

## Best Practices

### ✅ Do This
- Create `.claude/team-guidelines.md` for better quality
- Review PLAN.md before using prompts
- Use DEEP mode for risky/architectural changes
- Check breaking changes section
- Edit surgical prompts if needed (they're guides)

### ❌ Don't Do This
- Use for single-file "fix typo" changes (overkill)
- Trust the plan blindly (always review)
- Ignore breaking changes warnings
- Share PLAN.md with exposed secrets
- Use DEEP mode for simple bug fixes (wasteful)

---

## Common Patterns

### Feature Implementation
```bash
/code-surgeon "Add user authentication with JWT tokens"
```
Get: Current analysis, recommended approach, breaking changes, step-by-step tasks

### Bug Fix
```bash
/code-surgeon "https://github.com/org/repo/issues/1234" --depth=DEEP
```
Get: Root cause, affected files, why it happens, breaking changes

### Refactoring
```bash
/code-surgeon "Refactor auth module to remove dependency on X" --depth=DEEP
```
Get: Current architecture, alternatives, file relationships, migration strategy

---

For more details, see:
- [SKILL.md](../SKILL.md) - Complete technical reference
- [EXAMPLES.md](EXAMPLES.md) - Real-world use cases
- [ARCHITECTURE.md](ARCHITECTURE.md) - How it works internally
