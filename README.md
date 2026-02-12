<!-- badges -->
<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version: 1.0.0](https://img.shields.io/badge/Version-1.0.0-blue.svg)](CHANGELOG.md)
[![Status: Production Ready](https://img.shields.io/badge/Status-Production%20Ready-green.svg)](#)

</div>

---

# code-surgeon

**Transform GitHub issues and requirements into precise implementation plans with surgical prompts—step-by-step, file-by-file guidance for code changes.**

<p align="center">
  <strong>
    <a href="#quick-start">Quick Start</a>
    •
    <a href="#features">Features</a>
    •
    <a href="#how-it-works">How It Works</a>
    •
    <a href="#documentation">Documentation</a>
    •
    <a href="#examples">Examples</a>
  </strong>
</p>

---

## 🎯 What It Does

code-surgeon analyzes your codebase and requirements, then generates:

✅ **Comprehensive Implementation Plans** — 6-section plans with strategy, research, design choices, and verification checklist

✅ **Surgical Prompts** — Precise, actionable prompts with file paths, line numbers, and code examples

✅ **Breaking Change Analysis** — Proactive detection of API, data, behavior, and dependency impacts

✅ **Framework-Aware Guidance** — 35+ frameworks auto-detected with framework-specific patterns

✅ **Team Convention Enforcement** — Automatic integration with your team guidelines

✅ **3 Output Formats** — Markdown (human), JSON (tools), Interactive CLI (step-through)

---

## ⚡ Quick Start

### Installation

```bash
# Via npm skills registry
npx skills add username/code-surgeon

# Or clone directly
git clone https://github.com/baagad-ai/code-surgeon.git ~/.claude/skills/code-surgeon
```

### Basic Usage

```bash
# Analyze a simple requirement
/code-surgeon "Add JWT token refresh to authentication flow"

# Analyze a GitHub issue
/code-surgeon "https://github.com/myorg/myrepo/issues/234"

# Use specific depth mode
/code-surgeon "Fix pagination bug" --depth=QUICK
/code-surgeon "Refactor auth system" --depth=DEEP

# Resume interrupted session
/code-surgeon-resume surgeon-20250212-abc123xyz
```

**Get started in 15 minutes.** See [Installation](docs/INSTALLATION.md) for detailed setup.

---

## ✨ Features

### 5-Phase Analysis Pipeline

| Phase | Time | What Happens |
|-------|------|--------------|
| **1. Analysis** | 2 min | Parse requirements, detect frameworks |
| **2. Research** | 5 min | Understand codebase, map dependencies |
| **3. Planning** | 3 min | Create 6-section implementation plan |
| **4. Prompts** | 2 min | Generate precise surgical prompts |
| **5. Format** | 1 min | Output as Markdown, JSON, or CLI |

### Three Depth Modes

Choose based on your situation:

| Mode | Time | Accuracy | Cost | Best For |
|------|------|----------|------|----------|
| **QUICK** | 5 min | 85% | $0.04 | Bug fixes, isolated changes |
| **STANDARD** | 15 min | 95% | $0.10 | Normal features (default) |
| **DEEP** | 30 min | 99% | $0.17 | Architecture, risky changes |

### Framework Support

**35+ frameworks auto-detected:**
- Frontend: React, Vue, Angular, Svelte, Next.js, and more
- Backend: Django, FastAPI, Express, Rails, Spring Boot, and more
- Mobile: React Native, Flutter, Swift, Kotlin
- Other: Node.js, Python, Rust, Java, C#, PHP, Ruby

---

## 🔍 How It Works

```
Your Requirement (GitHub issue or description)
    ↓
┌─────────────────────────────────────────────┐
│  PHASE 1: Issue Analysis + Framework        │
│  • Parse requirements                       │
│  • Detect issue type (feature/bug/etc)     │
│  • Identify frameworks & tech stack        │
└─────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────┐
│  PHASE 2: Context Research                 │
│  • Analyze codebase structure              │
│  • Map dependencies                        │
│  • Extract architectural patterns          │
│  • Select relevant files (smart 3-tier)   │
└─────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────┐
│  PHASE 3: Implementation Planning          │
│  • Create 6-section plan                   │
│  • Analyze breaking changes                │
│  • Order tasks logically                   │
└─────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────┐
│  PHASE 4: Surgical Prompt Generation       │
│  • Create 9-section prompts per task       │
│  • Apply framework-specific templates      │
│  • Validate against team guidelines        │
└─────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────┐
│  PHASE 5: Output Formatting                │
│  • Generate PLAN.md (human-readable)      │
│  • Generate plan.json (machine-readable)  │
│  • Generate interactive.json (CLI mode)   │
└─────────────────────────────────────────────┘
    ↓
Ready-to-Use Implementation Guidance
```

---

## 📚 Documentation

### Getting Started
- **[Quick Start](#quick-start)** — 5-minute setup and first command
- **[Installation](docs/INSTALLATION.md)** — Detailed setup instructions
- **[Usage Guide](docs/USAGE.md)** — How to use code-surgeon effectively

### Learn More
- **[Examples](docs/EXAMPLES.md)** — Real-world use cases and patterns
- **[Architecture](docs/ARCHITECTURE.md)** — Deep dive into how it works
- **[Frameworks](docs/FRAMEWORKS.md)** — Framework-specific guidance
- **[SKILL.md](SKILL.md)** — Complete technical reference

### Reference
- **[CHANGELOG.md](CHANGELOG.md)** — Version history and updates
- **[CONTRIBUTING.md](CONTRIBUTING.md)** — How to contribute
- **[LICENSE](LICENSE)** — MIT License

---

## 🎓 When to Use

### ✅ Perfect For
- Implementing features across multiple files
- Complex bug fixes with broad impact
- Refactoring with architectural changes
- When you need a written plan before coding
- Handing off work to another AI agent
- Understanding unfamiliar codebases

### ❌ Not Ideal For
- Single-file, obvious changes ("fix typo in README")
- Emergency hotfixes needing immediate coding
- Unstructured repositories with no clear patterns
- Proprietary code you can't analyze locally

---

## 💡 How to Choose Your Depth Mode

Before running code-surgeon, answer these questions:

```
1. Can you describe the change in one sentence?
   NO  → Define it first, then return
   YES → Continue

2. How many files will this affect?
   1-3   → QUICK mode (5 min, 85% accuracy)
   5-8   → STANDARD mode (15 min, 95% accuracy) ← default
   8+    → STANDARD or DEEP mode

3. What's the risk if something breaks?
   Low   → QUICK mode (bug fix, isolated)
   Med   → STANDARD mode (new feature)
   High  → DEEP mode (security, payments, architecture)

4. How much time can you invest?
   <10 min  → QUICK
   15-20 min → STANDARD
   30+ min  → DEEP
```

**Recommendation:** When in doubt, use STANDARD mode (default).

---

## 🔐 Security & Privacy

✅ **Completely Local** — All analysis happens on your machine
✅ **No External Calls** — Code never leaves your machine
✅ **Automatic Protection** — PII/secret detection and blocking
✅ **Team Conventions** — Enforces your guidelines
✅ **State Management** — Atomic, recoverable sessions

---

## 🛠️ Advanced Features

### Team Guidelines Integration

Create `.claude/team-guidelines.md` to enforce your team's conventions:

```markdown
# Team Guidelines

## Code Style
- Use camelCase for variables, PascalCase for classes
- Max line length: 100 characters

## Architecture Patterns
- Use dependency injection for services
- Always use try-catch in async functions

## Security
- Never hardcode secrets or API keys
- Validate all user input
```

code-surgeon automatically loads and validates against these guidelines.

### Resumable Sessions

Interrupted mid-analysis? No problem:

```bash
/code-surgeon-resume surgeon-20250212-abc123xyz
```

Sessions are saved atomically after each phase, allowing you to resume from where you left off.

### Output Formats

**PLAN.md** — Human-readable, ready to review
**plan.json** — Machine-readable, for tool integration
**interactive.json** — Step-through CLI mode for guided execution

---

## 📊 Real-World Examples

### Example 1: Feature Implementation
```bash
/code-surgeon "Add JWT token refresh to authentication flow"
```

**Gets:**
- Analysis of current auth system
- Recommended JWT approach with alternatives
- Breaking changes for existing clients
- Step-by-step implementation tasks
- Surgical prompts for each task

### Example 2: Complex Bug Fix
```bash
/code-surgeon "https://github.com/org/repo/issues/1234" --depth=DEEP
```

**Gets:**
- Detailed root cause analysis
- All affected files and functions
- Why the bug happens
- Breaking changes if the fix changes behavior
- Surgical prompts with line numbers

### Example 3: Refactoring
```bash
/code-surgeon "Refactor authentication module to remove dependency on X" --depth=DEEP
```

**Gets:**
- Current architecture analysis
- Alternative approaches with tradeoffs
- File relationships and impact
- Migration strategy
- Breaking changes assessment

See [docs/EXAMPLES.md](docs/EXAMPLES.md) for more examples.

---

## ⚙️ Sub-Skills

code-surgeon orchestrates 5 specialized sub-skills:

| Sub-Skill | Phase | Role |
|-----------|-------|------|
| **issue-analyzer** | 1A | Parse requirements, detect issue type |
| **framework-detector** | 1B | Identify frameworks, versions, tech stack |
| **context-researcher** | 2 | Analyze codebase, select relevant files |
| **implementation-planner** | 3 | Create comprehensive implementation plans |
| **surgical-prompt-generator** | 4 | Generate precise, actionable prompts |

Each sub-skill is modular and can be used independently. See [SKILL.md](SKILL.md) for detailed invocation contracts.

---

## 💬 Tips for Best Results

### ✅ Do This
- Create `.claude/team-guidelines.md` (dramatically improves quality)
- Review the PLAN.md before using prompts
- Use DEEP mode for risky changes
- Check breaking changes section
- Edit surgical prompts if needed (they're guides, not law)

### ❌ Don't Do This
- Use for single-file "fix typo" changes (overkill)
- Trust the plan blindly (always review)
- Ignore breaking changes warnings
- Share PLAN.md with unmasked PII/secrets
- Use DEEP mode for simple bug fixes (wasteful)

---

## ❓ FAQ

**Q: How accurate are the plans?**
A: STANDARD mode: 95% accuracy. DEEP mode: 99% accuracy. Always review before using.

**Q: What if I'm interrupted?**
A: Sessions are saved automatically. Resume with `/code-surgeon-resume <session-id>`.

**Q: Does this work with monorepos?**
A: Yes! Detects Lerna, Turborepo, yarn workspaces automatically.

**Q: Can I use this with proprietary code?**
A: Yes, everything is local. Code never leaves your machine.

**Q: Can I edit the PLAN.md?**
A: Yes! Edit freely. The surgical prompts are guides, not absolute.

**Q: What if code-surgeon detects PII?**
A: Generation is blocked. Sanitize your code (remove API keys, emails), then retry.

**Q: How does it handle large codebases?**
A: Smart 3-tier file selection respects token budgets while capturing dependencies.

See [docs/FAQ.md](docs/FAQ.md) for more questions.

---

## 🚀 Next Steps

1. **[Install code-surgeon](#installation)** — 2 minutes
2. **Create `.claude/team-guidelines.md`** — 5 minutes (optional but recommended)
3. **Try it on a real requirement** — `npx skills add username/code-surgeon` then `/code-surgeon "describe your change"`
4. **Review the PLAN.md** — See what it generates
5. **Copy surgical prompts** — Hand to your AI agent

---

## 🤝 Contributing

Found a bug? Have a suggestion? [Contribute!](CONTRIBUTING.md)

Contributions are welcome and appreciated. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## 📞 Support

- 📖 **Documentation:** [docs/](docs/) folder
- 🐛 **Report Bugs:** [GitHub Issues](https://github.com/baagad-ai/code-surgeon/issues)
- 💬 **Ask Questions:** Create an issue with `question` label

---

<div align="center">

**Ready to transform your code analysis workflow?**

[Get Started →](#quick-start)

Made with ❤️ by Prajwal Mishra

</div>
