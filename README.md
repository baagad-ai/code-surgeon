<!-- badges -->
<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version: 1.2.0](https://img.shields.io/badge/Version-1.2.0-blue.svg)](CHANGELOG.md)
[![Status: Enterprise Ready](https://img.shields.io/badge/Status-Enterprise%20Ready-brightgreen.svg)](#)

</div>

---

# code-surgeon v1.2

**Stop analyzing code manually. Ask code-surgeon to understand your codebase, review changes for safety, find performance issues, or create step-by-step implementation guides.**

A multi-mode development advisor that handles 4 different tasks:

- 🔍 **Understand** your codebase architecture and patterns
- ✅ **Review** changes before implementing them
- ⚡ **Optimize** performance and security
- 📋 **Plan** implementations with step-by-step guidance

---

## 🎯 What You Can Do

### 1. Understand Your Codebase
New to a project? Need to understand how things work?

```bash
/code-surgeon --mode=discovery
```

Get a report on:
- System architecture and how components fit together
- Tech stack and framework versions
- Design patterns you're using
- Potential security and technical risks
- What you need to know before making changes

**Perfect for:** Onboarding, code reviews, pre-refactor analysis

---

### 2. Review Changes Before Building
Planning a major change but worried about what might break?

```bash
/code-surgeon "Rename authentication module to auth-service" --mode=review
```

Get a safety assessment:
- What files will be affected
- Will this break existing code? (what needs to change)
- Is this safe? What validation is needed?
- Step-by-step migration guide for dependent code

**Perfect for:** Large refactors, dependency upgrades, architecture changes, pre-merge validation

---

### 3. Find Performance & Security Issues
Code running slow? Want to harden security?

```bash
/code-surgeon --mode=optimization
```

Identify:
- Slow database queries and N+1 problems
- Memory leaks and inefficient patterns
- Security vulnerabilities (SQL injection, XSS, etc.)
- Outdated dependencies with known CVEs
- Code complexity hotspots
- Ranked by impact—fix the important stuff first

**Perfect for:** Performance tuning, security audits, technical debt cleanup, code quality improvements

---

### 4. Plan Implementations Step-by-Step
Ready to build? Get a detailed plan before coding.

```bash
/code-surgeon "Add JWT token refresh to authentication"
```

Get:
- High-level strategy and architecture approach
- List of files you'll need to change
- Step-by-step tasks (test-driven, commit-friendly)
- Code examples for each change
- What might break and how to handle it
- Testing and verification checklist

**Perfect for:** Feature implementation, bug fixes, refactoring with confidence

---

## ⚡ Quick Start

### Installation

```bash
# Via skills.sh
npx skills add baagad-ai/code-surgeon

# Or clone to skills directory
git clone https://github.com/baagad-ai/code-surgeon.git ~/.claude/skills/code-surgeon
```

### First Command

```bash
# Pick ONE based on what you need:

# 1. Understand the codebase
/code-surgeon --mode=discovery

# 2. Check if a change is safe
/code-surgeon "Update React from v17 to v18" --mode=review

# 3. Find things to improve
/code-surgeon --mode=optimization

# 4. Plan a feature (default mode)
/code-surgeon "Add email notifications on user signup"
```

### Speed Options

```bash
# Quick (5 min, good enough for small changes)
/code-surgeon "Fix bug in login" --depth=QUICK

# Standard (15 min, recommended for normal work)
/code-surgeon "Add payment processing"

# Deep (30 min, for risky or complex changes)
/code-surgeon "Refactor authentication system" --depth=DEEP
```

---

## 💡 When to Use Each Mode

| Need | Mode | Command |
|------|------|---------|
| Understand architecture | Discovery | `/code-surgeon --mode=discovery` |
| Validate a change | Review | `/code-surgeon "describe change" --mode=review` |
| Find issues | Optimization | `/code-surgeon --mode=optimization` |
| Plan implementation | Implementation | `/code-surgeon "describe task"` |

**Unsure?** See [docs/USAGE.md](docs/USAGE.md) for detailed guidance.

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

## 🎓 Real Examples

### Example 1: New Team Member Onboarding

```bash
$ /code-surgeon --mode=discovery
```

**You get:**
- Architecture overview showing main components
- Tech stack explanation (frameworks, databases, languages)
- Key design patterns in the codebase
- Known issues and risks
- Where to start reading code

**Result:** You understand the system without asking 50 questions.

---

### Example 2: Pre-Merge Code Review

```bash
$ /code-surgeon "Large refactoring of auth module" --mode=review --depth=DEEP
```

**You get:**
- What exactly changes and why
- Everything that depends on the auth module
- Will this break anything? If yes, what needs to change?
- Testing checklist before merge
- Risk assessment and sign-off

**Result:** Merge with confidence, knowing nothing breaks.

---

### Example 3: Performance Investigation

```bash
$ /code-surgeon --mode=optimization
```

**You get:**
- This API endpoint is slow because of [specific reason]
- This database query runs 1000x more times than needed (N+1)
- This code is vulnerable to SQL injection
- Bundle size is 200KB larger than it needs to be
- Ranked by impact (fix the critical stuff first)
- Estimated improvement and effort to fix each

**Result:** Know exactly what to optimize and why.

---

### Example 4: Feature Implementation

```bash
$ /code-surgeon "Add two-factor authentication via SMS"
```

**You get:**
- Overview: How 2FA fits into existing auth system
- Files you'll need to change (with specific line numbers)
- Database schema changes needed
- Step 1: Add SMS provider integration
- Step 2: Create 2FA code generation logic
- Step 3: Add UI for user to enter code
- ...and so on, one testable step at a time
- Checklist: What to test, what might break, verification steps

**Result:** Build with a clear plan, commit cleanly, no surprises.

---

## 🚀 What Happens Behind the Scenes

1. **Analyzes your codebase** — reads files, maps dependencies, detects frameworks
2. **Understands context** — learns your architecture, patterns, team conventions
3. **Generates guidance** — based on what you asked and what the code needs
4. **Formats output** — as readable text, JSON for tools, or step-by-step CLI

**All locally.** Nothing leaves your machine.

---

## ❓ FAQ

**Q: How long does it take?**
A: QUICK mode (5 min), STANDARD mode (15 min), or DEEP mode (30 min). Choose based on complexity.

**Q: Does it work with my tech stack?**
A: React, Vue, Angular, Next.js, Django, Rails, Express, FastAPI, Go, Rust, Python, Java, C#, and 25+ others.

**Q: What if I'm interrupted?**
A: Sessions auto-save. Resume with `/code-surgeon-resume <session-id>`.

**Q: Can I edit the output?**
A: Yes! It's a guide, not law. Edit as needed for your situation.

**Q: What if it detects secrets in my code?**
A: Generation is blocked. Sanitize API keys and passwords, then retry.

**Q: Does monorepo work?**
A: Yes, it detects Lerna, Turborepo, Yarn workspaces automatically.

See [docs/FAQ.md](docs/FAQ.md) for more.

---

## 💬 Tips for Best Results

### ✅ Do This
- Be specific: "Add JWT token refresh in auth module" (not just "fix auth")
- Create `.claude/team-guidelines.md` with your coding standards
- Review the output before using it (always use judgment)
- Use DEEP mode for risky changes
- Read the "what might break" section

### ❌ Don't Do This
- Single-file typo fixes ("remove console.log" doesn't need a plan)
- Trust blindly (always review, always think)
- Ignore breaking change warnings
- Share output that contains real API keys/secrets

---

## 🤝 Contributing

Found a bug? Have an idea? [Open an issue](https://github.com/baagad-ai/code-surgeon/issues).

---

## 📄 License

MIT License — use freely in personal and commercial projects.

---

## 🎯 Next Steps

1. **[Install code-surgeon](docs/INSTALLATION.md)** (2 min)
2. **Try it on your real codebase** — `/code-surgeon --mode=discovery`
3. **Read the output** — see what insights it provides
4. **Use it for your next task** — code review, performance fix, new feature

---

<div align="center">

**Ready to code with confidence?**

[📖 Read the Docs](docs/) • [❓ FAQ](docs/FAQ.md) • [💻 Examples](docs/EXAMPLES.md)

Made with ❤️ by Prajwal Mishra

</div>
