# Task Classification Framework

How to determine which code-surgeon mode is right for your task.

---

## The 5-Question Flowchart

Ask these questions in order to classify your task:

### Question 1: Do you have a clear requirement to implement?

**Question:** Can you describe what you want to build/change in clear terms?

```
YES → You have an implementation target
      Go to Question 2

NO → You don't have a specific change in mind yet
     → DISCOVERY MODE (understand the codebase first)
     → Run: /code-surgeon --mode=discovery
```

**When NO applies:**
- "I inherited this codebase and don't understand it"
- "I need to understand how authentication works"
- "What patterns does this project use?"
- "I need a high-level architecture overview"

---

### Question 2: Is your requirement about a new implementation (feature, bug fix, refactoring)?

**Question:** Do you need to BUILD something or FIX something?

```
YES → Implementation task
      → IMPLEMENTATION PLANNING MODE (default)
      → Run: /code-surgeon "your requirement"

NO → You have a requirement but it's about assessing/improving existing code
     Go to Question 3
```

**When YES applies:**
- "Add JWT token refresh to authentication"
- "Fix the off-by-one error in the pagination module"
- "Refactor the authentication system to use OAuth"
- "Add email notifications on order completion"

**When NO applies:**
- "Will updating to React 18 break our code?"
- "What's the impact of moving to TypeScript?"
- "Should we add caching here?"
- "This code is slow, how can we optimize it?"

---

### Question 3: Do you need to assess the IMPACT of a change before implementing?

**Question:** Are you asking "Is this safe?" or "Will this break things?"

```
YES → Assessment + safety check needed
      → REVIEW MODE
      → Run: /code-surgeon "your requirement" --mode=review

NO → You want to improve existing code without a major change
     Go to Question 4
```

**When YES applies:**
- "What breaks if we update to React 18?"
- "Is it safe to rename this authentication module?"
- "What's the impact of upgrading Express from v4 to v5?"
- "Will changing this API endpoint break clients?"
- "Should we merge this refactoring?" (pre-merge review)

**When NO applies:**
- "This code is inefficient, optimize it"
- "Find security vulnerabilities in our API"
- "This function is too complex, simplify it"

---

### Question 4: Do you want to improve/optimize existing code?

**Question:** Are you looking for performance gains, security improvements, or code quality enhancements?

```
YES → Performance/security/efficiency improvements
      → OPTIMIZATION MODE
      → Run: /code-surgeon --mode=optimization

NO → Unclear intent
     → ASK CLARIFYING QUESTIONS (see below)
```

**When YES applies:**
- "This endpoint is slow, optimize it"
- "Find security vulnerabilities"
- "Reduce bundle size"
- "This code is too complex, simplify it"
- "What dependencies can we remove?"

---

## Decision Tree Summary

```
Do you have a clear requirement?
├─ NO → Discovery mode (/code-surgeon --mode=discovery)
└─ YES
   └─ Is it about implementing/building/fixing?
      ├─ YES → Implementation Planning mode (/code-surgeon "requirement")
      └─ NO
         └─ Do you need impact assessment?
            ├─ YES → Review mode (/code-surgeon "requirement" --mode=review)
            └─ NO
               └─ Do you want to improve existing code?
                  ├─ YES → Optimization mode (/code-surgeon --mode=optimization)
                  └─ NO → Ask clarifying questions
```

---

## When to Ask Clarifying Questions

Sometimes the user's intent is ambiguous. **Before running code-surgeon, ask:**

### If the request is vague:

**User says:** "Fix our authentication."

**You ask:**
1. "What specifically is broken or needs changing?"
2. "Are you looking to redesign it, fix a bug, or add a feature?"
3. "Do you want me to understand the current system first, or do you already know what needs changing?"

**Better requests:**
- "Fix the JWT token refresh bug in src/auth/token-refresh.ts"
- "Refactor authentication to use OAuth2 instead of custom JWT"
- "I need to understand how our authentication system works"

---

### If the request could be multiple modes:

**User says:** "I want to update the payment system to use Stripe instead of our custom processor."

**You ask:**
1. "Is this a complete rewrite (implementation) or incremental migration (review/impact check)?"
2. "Do you want me to first understand the existing payment system?"
3. "Do you want me to assess what will break before you implement?"

**Clarified requests:**
- "I need to understand how our current payment system works" → Discovery
- "I'm planning to migrate to Stripe. What will break?" → Review
- "Create an implementation plan for Stripe integration" → Implementation Planning

---

### If the request mixes multiple concerns:

**User says:** "Our API is slow and insecure. Fix it."

**You ask:**
1. "Are you asking me to find the problems (analysis) or create a plan to fix them (implementation)?"
2. "Do you have a budget for refactoring, or are you looking for quick wins?"
3. "Should I focus on performance, security, or both?"

**Clarified requests:**
- "Find security vulnerabilities and performance bottlenecks" → Optimization
- "Create a plan to refactor the API for performance" → Implementation Planning
- "I'm considering a major API redesign. What are the risks?" → Review

---

## Mode Selection Quick Reference

### Discovery Mode
**Use when:** "I need to understand something"

**Indicators:**
- Unfamiliar codebase
- Needs architecture overview
- Needs tech stack assessment
- Needs to identify patterns
- Needs risk/security audit
- New team member onboarding

**Examples:**
- "I just joined the team. How does our system work?"
- "Audit our codebase for security issues"
- "What patterns does this project use?"
- "Understand how the payment flow works"

**Output:** Audit report with architecture, patterns, risks, findings

---

### Review Mode
**Use when:** "Will this change break anything?"

**Indicators:**
- Pre-implementation impact assessment
- Breaking change detection needed
- Safe refactoring validation
- Dependency upgrade concerns
- Pre-merge review
- Migration planning

**Examples:**
- "Is it safe to upgrade React to 18?"
- "What breaks if I rename this module?"
- "Assessment before merging this refactoring"
- "What's the impact of using TypeScript?"

**Output:** Risk report with breaking changes, pre-flight checklist, validation results

---

### Optimization Mode
**Use when:** "How can I improve this code?"

**Indicators:**
- Performance optimization needed
- Security hardening needed
- Code quality improvements
- Bundle size reduction
- Technical debt analysis
- Efficiency gains

**Examples:**
- "This endpoint is slow, optimize it"
- "Find security vulnerabilities"
- "Reduce bundle size"
- "This function is too complex"
- "What can we do about technical debt?"

**Output:** Optimization report with prioritized recommendations, metrics, rationale

---

### Implementation Planning Mode
**Use when:** "I know what I want to build/fix"

**Indicators:**
- Feature to implement
- Bug to fix
- Refactoring task
- Clear scope and goals
- Need surgical prompts
- Need step-by-step plan

**Examples:**
- "Add JWT token refresh"
- "Fix off-by-one error in pagination"
- "Refactor authentication to use OAuth"
- "Add email notifications"

**Output:** Implementation plan with phases, tasks, surgical prompts, breaking changes, verification checklist

---

## Depth Mode Selection

All modes support 3 depth levels. Choose based on:

### QUICK (5 minutes, 85% accuracy)
**Use when:**
- Simple, scoped changes
- Small codebase or isolated modules
- User says "I'm in a hurry"
- Low-risk changes
- Clear scope

**Example:**
```bash
/code-surgeon "Fix typo in auth.ts" --depth=QUICK
```

### STANDARD (15 minutes, 95% accuracy) ← DEFAULT
**Use when:**
- Normal features and bug fixes
- Moderate scope (5-10 files affected)
- Most real-world changes
- When unsure, default to STANDARD

**Example:**
```bash
/code-surgeon "Add JWT token refresh"
# or
/code-surgeon "Add JWT token refresh" --depth=STANDARD
```

### DEEP (30 minutes, 99% accuracy)
**Use when:**
- Architectural changes
- Risky broad-impact work
- Security/payment system changes
- User says "I need high confidence"
- Complex multi-system changes

**Example:**
```bash
/code-surgeon "Refactor authentication system" --depth=DEEP
/code-surgeon --mode=discovery --depth=DEEP
```

---

## Common Misclassifications & Fixes

### ❌ "Refactor the API" (too vague)
**Problem:** Could be optimization, review, or implementation

**Fix:** Ask:
- "What specifically should change?" → Implementation Planning or Optimization
- "Are you concerned about breaking changes?" → Review
- "Need to understand it first?" → Discovery

---

### ❌ "I want to use Kafka instead of RabbitMQ" (seems like implementation, but risky)
**Problem:** Sounds like implementation, but critical architectural change needs review first

**Fix:** Two-step approach:
1. First: `/code-surgeon "Use Kafka instead of RabbitMQ" --mode=review`
2. Then: `/code-surgeon "Implement Kafka migration" --mode=implementation` (if review is OK)

---

### ❌ "Make the code faster and more secure" (mixed concerns)
**Problem:** Too broad, needs narrowing

**Fix:** Ask:
- "Are you finding specific problems or looking for opportunities?" → Optimization
- "Do you have a specific area in mind?" → Optimization mode on that area
- "Are you planning changes?" → Implementation Planning after optimization analysis

---

### ❌ "Understand this codebase" (vague discovery)
**Problem:** Too broad, needs focus

**Fix:** Clarify:
- "Focus on architecture, patterns, security, or tech stack?"
- "Are there specific modules you want to understand?"
- Run discovery with `--depth=STANDARD` to get balanced analysis

---

## When Unsure

**Default to this priority:**

1. **If building/fixing:** Implementation Planning
2. **If assessing risk:** Review
3. **If understanding:** Discovery
4. **If improving:** Optimization
5. **If still unclear:** Ask clarifying questions above

---

## Post-Classification Checklist

Before running `/code-surgeon`, verify:

- ✅ Task is clearly defined (not vague)
- ✅ Mode matches the intent (use flowchart above)
- ✅ Depth level is appropriate (QUICK/STANDARD/DEEP)
- ✅ For Implementation Planning and Review: requirement is provided
- ✅ For Discovery and Optimization: repo path is accessible
- ✅ User expectations are aligned with mode output
