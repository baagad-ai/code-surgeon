<!-- badges -->
<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version: 1.2.0](https://img.shields.io/badge/Version-1.2.0-blue.svg)](CHANGELOG.md)
[![Status: Enterprise Ready](https://img.shields.io/badge/Status-Enterprise%20Ready-brightgreen.svg)](#)

</div>

---

# code-surgeon

**Stop analyzing code manually. Ask code-surgeon to understand your codebase, review changes for safety, find performance issues, or plan implementations—all with surgical precision.**

## What It Does (In 30 Seconds)

code-surgeon is a multi-mode AI advisor that transforms how you work with code. Ask it to understand a system, validate a change before building it, optimize for performance and security, or create step-by-step implementation plans.

It reads your codebase, understands your architecture and patterns, then gives you specific, actionable guidance—not generic advice. No upload, no API keys, everything stays on your machine.

**4 powerful modes. One command. Complete confidence.**

---

## The 4 Modes: Choose Your Superpower

Pick ONE based on what you need right now:

### 🔍 **Discovery Mode** — Understand Your Codebase
*"I'm new here. What should I know?"*

```bash
/code-surgeon --mode=discovery
```

**You get:**
- System architecture: How components fit together
- Tech stack and versions: What you're building with
- Design patterns: How code is organized
- Known risks: Security and technical issues to watch
- Learning path: Where to start reading

**Perfect for:** Onboarding, code reviews, pre-refactor analysis, security audits

---

### ✅ **Review Mode** — Validate Changes Before Building
*"Is this refactor safe? What could break?"*

```bash
/code-surgeon "Rename auth module to auth-service" --mode=review
```

**You get:**
- Impact analysis: Exactly which files are affected
- Breaking changes: What will break and how to fix it
- Safety verdict: Go/no-go with explicit conditions
- Pre-flight checklist: Everything to validate before merging
- Migration guide: Step-by-step instructions for dependent code

**Perfect for:** Large refactors, dependency upgrades, architectural changes, pre-merge validation

---

### ⚡ **Optimization Mode** — Find Performance & Security Issues
*"Where are our bottlenecks? What's vulnerable?"*

```bash
/code-surgeon --mode=optimization
```

**You get:**
- Performance bottlenecks: Slow queries, render issues, inefficient code (ranked by impact)
- Security vulnerabilities: SQL injection, XSS, auth gaps, CVE dependencies
- Code quality improvements: Complexity, duplication, dead code
- Prioritized roadmap: What to fix first, effort vs impact

**Perfect for:** Performance tuning, security hardening, technical debt cleanup

---

### 📋 **Implementation Planning** — Build Step-by-Step
*"I know what I want. How do I build it?"*

```bash
/code-surgeon "Add JWT token refresh to authentication"
```

**You get:**
- Architecture approach: How this fits into your system
- File-by-file changes: Exact locations and modifications
- Step-by-step tasks: Each one testable, each one committable
- Code examples: Copy-paste starting points
- Breaking change analysis: What might break and how to handle it
- Testing checklist: What to verify before shipping

**Perfect for:** Feature implementation, bug fixes, refactoring with confidence

Each mode runs independently. Use whichever matches your current need. No setup, no config, just results.

---

## See It In Action — Real Examples

### Example 1: New Team Member (Discovery Mode)

You just joined a React + Express team. Day one:

```bash
$ /code-surgeon --mode=discovery
```

**You immediately know:**
- Frontend: React 18 with custom hooks pattern for state
- Backend: Express with layered architecture (routes → services → database)
- Database: PostgreSQL with Sequelize ORM
- Main risk: N+1 query problem in user fetching, outdated dependency (lodash 4.17.19 with known CVE)
- Where to start: Auth module is the beating heart — 12 other files depend on it

**Result:** Instead of asking 50 questions, you understand the system in 5 minutes.

---

### Example 2: Pre-Merge Code Review (Review Mode)

Your team wants to refactor the authentication module. Before merging:

```bash
$ /code-surgeon "Refactor auth module" --mode=review --depth=DEEP
```

**You get:**
- 15 files will be affected
- 3 critical breaking changes (function signatures, return types, error handling)
- 12 tests will need updates
- External dependency: `@company/auth-utils` package depends on this — users need migration guide
- Go/no-go verdict: **SAFE TO MERGE** IF checklist completed

**Result:** You catch issues BEFORE code review, not during merge hell.

---

### Example 3: Performance Investigation (Optimization Mode)

Users complain the user listing endpoint is slow:

```bash
$ /code-surgeon --mode=optimization
```

**You discover:**
- **Critical (fix now):** N+1 query fetching posts per user (200 queries instead of 1 join)
- **High (fix this sprint):** Response is 2MB uncompressed, could be 400KB with gzip
- **Medium (schedule later):** React component re-renders on every list update
- **ROI:** Fix the N+1 query → 10x faster (1000ms → 100ms), 30 minutes effort

**Result:** You know exactly what to optimize and why. No guessing.

---

### Example 4: Feature Implementation (Implementation Planning)

Build two-factor authentication:

```bash
$ /code-surgeon "Add SMS two-factor authentication"
```

**You get:**
- System overview: Where 2FA fits in your auth flow
- Step-by-step tasks (each one is 2-5 minutes of work):
  - Task 1: Write test for SMS code generation
  - Task 2: Implement code generation logic
  - Task 3: Add SMS provider integration
  - Task 4: Update auth flow with verification step
  - Task 5: Add UI for code entry
  - ...and so on
- Code examples: Starting point for each task
- Testing checklist: What to verify before shipping
- What might break: Legacy clients without 2FA support

**Result:** You build with a clear plan. No surprises. Easy commits.

---

## Quick Start — Get Running in 60 Seconds

### Installation

Choose one:

**Via skills.sh (easiest):**
```bash
npx skills add baagad-ai/code-surgeon
```

**Or clone directly:**
```bash
git clone https://github.com/baagad-ai/code-surgeon.git ~/.claude/skills/code-surgeon
```

Done. You're ready.

---

### Your First Command

Pick ONE based on what you need:

```bash
# 1. Understand a codebase you just joined
/code-surgeon --mode=discovery

# 2. Check if a change is safe before building it
/code-surgeon "Update React from v17 to v18" --mode=review

# 3. Find performance and security issues
/code-surgeon --mode=optimization

# 4. Plan a feature step-by-step (default mode, just describe what you want)
/code-surgeon "Add email notifications on signup"
```

That's it. Run whichever matches your current problem.

---

### Speed Options

All modes support three speeds — pick based on urgency:

```bash
# QUICK (5 min, good enough for small changes)
/code-surgeon "Fix bug in login" --depth=QUICK

# STANDARD (15 min, recommended for normal work) ← DEFAULT
/code-surgeon "Add payment processing"

# DEEP (30 min, for risky or complex changes)
/code-surgeon "Refactor authentication system" --depth=DEEP
```

---

## Decision Guide: Which Mode Should You Use?

| Situation | Mode | Command | Time |
|-----------|------|---------|------|
| **New to a codebase** — You need to understand the system | Discovery | `/code-surgeon --mode=discovery` | 15 min |
| **Planning a change** — You want to know what breaks | Review | `/code-surgeon "describe change" --mode=review` | 15 min |
| **Code is slow/risky** — You need to find issues and prioritize fixes | Optimization | `/code-surgeon --mode=optimization` | 15 min |
| **Ready to build** — You know what you want, need step-by-step guidance | Implementation | `/code-surgeon "describe task"` | 15 min |

**Unsure?** Ask yourself: *What problem am I trying to solve right now?*

- **"I don't understand this codebase"** → Discovery
- **"I'm worried this change will break things"** → Review
- **"This system needs to be faster/safer"** → Optimization
- **"I know what to build, need a plan"** → Implementation

### Real Workflow Example

```
Monday: New feature request arrives
↓
/code-surgeon "Add two-factor auth" --mode=review
(Is this safe? How much work? What breaks?)
↓
/code-surgeon "Add two-factor auth" --mode=optimization
(Are there security issues we should fix first?)
↓
/code-surgeon "Add two-factor auth"
(Get step-by-step implementation plan)
↓
Build with confidence using the plan
```

---

## ⚠️ How NOT to Use It — Avoid These Pitfalls

### ❌ Single-Line Changes
**Don't use for:** Fixing a typo, removing one console.log, changing a variable name
**Why:** Overkill. Just edit it directly.
**Exception:** If the change touches 5+ files or you're unsure about impact → use Review mode

### ❌ Simple, Obvious Tasks
**Don't use for:** "Add a button to the UI", "Increase timeout from 5s to 10s"
**Why:** Doesn't need deep analysis. Trust your knowledge.
**Exception:** If you're in an unfamiliar codebase or want to validate the change won't break things → use Review mode

### ❌ Code With Exposed Secrets
**Don't use:** If your code has hardcoded API keys, passwords, or credentials
**Why:** code-surgeon will block analysis to protect your secrets. Sanitize first, then retry.
**How:** Remove API keys, replace with `REDACTED` or environment variables, then analyze

### ❌ Trusting Output Blindly
**Don't:** Copy-paste code examples without understanding them
**Don't:** Skip testing the recommendations
**Don't:** Ignore the "might break" warnings
**Do:** Review the suggestions, adapt to your situation, test thoroughly

### ❌ Expecting Magic
**Don't:** Expect code-surgeon to write perfect, production-ready code
**What it does:** Provides guidance and starting points
**What you do:** Implement thoughtfully, test thoroughly, review carefully

### ✅ When Code-Surgeon SHINES

- You're about to make a change and want to validate it first
- You're onboarding to a new codebase and need understanding fast
- Your system is slow/vulnerable and you need to find and prioritize issues
- You're building a feature and want a detailed step-by-step plan
- You need to explain a change's impact to your team

---

## Frequently Asked Questions

### ⏱️ How long does it take?

- **QUICK mode:** 5 minutes (good enough for small changes)
- **STANDARD mode:** 15 minutes (recommended, most common)
- **DEEP mode:** 30 minutes (for risky or complex changes)

Pick based on urgency, not complexity.

---

### 🛠️ Does it work with my tech stack?

Yes. code-surgeon supports:

**Frontend:** React, Vue, Angular, Next.js, Svelte, Remix
**Backend:** Express, Django, FastAPI, Rails, Spring Boot, Go, Rust
**Databases:** PostgreSQL, MySQL, MongoDB, Firebase
**Languages:** TypeScript, JavaScript, Python, Go, Rust, Java, C#, and 20+ others

If your stack isn't listed, code-surgeon still works — it may just have less framework-specific guidance.

---

### 🔒 Does it upload my code somewhere?

**No.** Everything runs locally on your machine. Your code never leaves your computer. No API keys, no accounts, no data collection.

---

### ⚡ What if I get interrupted?

Sessions auto-save. If code-surgeon is interrupted, you can resume:

```bash
/code-surgeon-resume <session-id>
```

Pick up exactly where you left off.

---

### 📝 Can I edit the output?

Yes. The plan/analysis is a guide, not law. Edit as needed for your situation. code-surgeon provides direction; you apply judgment.

---

### 🔍 What if it detects secrets in my code?

Generation stops. Sanitize API keys and passwords, then retry. This protects you.

---

### 📦 Does monorepo work?

Yes. code-surgeon detects Lerna, Turborepo, Yarn workspaces automatically. Analyzes across all packages.

---

### 🤔 Why should I trust this?

- **Explicit reasoning:** Every recommendation explains the "why"
- **Breaking change warnings:** Tells you what could fail
- **Edge cases documented:** Known limitations are called out
- **You decide:** code-surgeon advises; you approve and implement
- **Review before using:** Always review suggestions against your knowledge

---

## How It Works — Under The Hood

When you run a command, code-surgeon follows a standardized process:

### The Analysis Process

```
┌─ Your Question ─┐
│  "Add JWT       │
│   refresh"      │
└────────┬────────┘
         │
         ↓
    ┌──────────────────────────────┐
    │  Phase 1: Framework Detection │ (2 min)
    │  Detects: Tech stack, versions│
    └──────────┬───────────────────┘
               │
               ↓
    ┌──────────────────────────────┐
    │  Phase 2: Context Research    │ (5 min)
    │  Analyzes: Files, dependencies│
    └──────────┬───────────────────┘
               │
               ↓
    ┌──────────────────────────────┐
    │  Phase 3: Deep Analysis       │ (3 min)
    │  Mode-specific deep dive      │
    │  - Discovery: Maps architecture
    │  - Review: Analyzes impact
    │  - Optimization: Finds issues
    │  - Implementation: Plans tasks
    └──────────┬───────────────────┘
               │
               ↓
    ┌──────────────────────────────┐
    │  Phase 4-6: Synthesis & Output│ (5 min)
    │  Generates: Guidance, code,   │
    │  step-by-step tasks           │
    └──────────┬───────────────────┘
               │
               ↓
    ┌─── Your Result ──┐
    │ Detailed analysis,│
    │ code examples,    │
    │ action plan       │
    └──────────────────┘
```

---

### What Makes It Work

**Smart file selection:** code-surgeon doesn't read your entire codebase (slow, wasteful). It intelligently selects the most relevant files based on your question.

**Pattern recognition:** Learns your architecture, conventions, and team patterns. Gives guidance that fits your system, not generic advice.

**Breaking change detection:** Automatically identifies what might break when you make changes, then provides migration paths.

**No hallucination:** Uses strict contracts with each analysis step to prevent made-up answers. If uncertain, says so.

**Local only:** Everything happens on your machine. No uploads, no API dependency, no privacy concerns.

---

### Why This Matters

Traditional code analysis tools either:
- Are too generic ("use a linter")
- Require setup and configuration
- Depend on external APIs
- Don't understand your specific architecture

code-surgeon is different:
- **Understands YOUR system** — analyzes your actual code
- **Zero setup** — run one command, get insights
- **Completely local** — secure and private
- **Actionable guidance** — specific to your codebase, not generic advice

---

## 🔒 Your Code Stays Private

- **Local Only** — Everything runs on your machine
- **No Upload** — Your code never leaves your computer
- **No Account** — No sign-up, no API keys needed
- **Open Source** — See exactly what it does

---

## 📚 Documentation

- **[USAGE.md](docs/USAGE.md)** — When to use each mode and how
- **[EXAMPLES.md](docs/EXAMPLES.md)** — Real-world usage examples
- **[FAQ.md](docs/FAQ.md)** — Common questions answered
- **[INSTALLATION.md](docs/INSTALLATION.md)** — Setup and troubleshooting
- **[SKILL.md](SKILL.md)** — Complete technical reference (for developers)

---

## Ready to Code With Confidence?

### 🚀 Get Started Now

**Pick your first use case:**

```bash
# New to a codebase?
/code-surgeon --mode=discovery

# Planning a big change?
/code-surgeon "Your change description" --mode=review

# Code needs optimization?
/code-surgeon --mode=optimization

# Ready to build?
/code-surgeon "Your feature description"
```

No installation needed. It's already here. Just run a command.

---

## 🤝 Questions or Feedback?

Found a bug? Have an idea? [Open an issue](https://github.com/baagad-ai/code-surgeon/issues) on GitHub.

---

## ✨ What Makes code-surgeon Different

| Traditional | code-surgeon |
|------------|-------------|
| Generic advice | Specific to YOUR codebase |
| Manual analysis | Automated, instant |
| External dependencies | Local, no setup |
| One-size-fits-all | Adapts to your patterns |
| Slow and tedious | 5-30 minutes, any complexity |

---

## The Vision

**Stop analyzing code manually.** Stop wondering if changes are safe. Stop guessing where bottlenecks are. Stop staring at implementation plans.

Ask code-surgeon. Get surgical precision. Ship with confidence.

---

Made with ❤️ for developers who want to move faster

[License: MIT](LICENSE) • [Version: 1.2.0](CHANGELOG.md) • [Status: Enterprise Ready](#)
