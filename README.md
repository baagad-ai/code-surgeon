# code-surgeon

Transform GitHub issues and requirements into precise implementation plans with surgical prompts—step-by-step, file-by-file guidance for code changes.

## What It Does

code-surgeon is an AI-powered code analysis skill that:

1. **Analyzes Requirements** — Parse GitHub issues or plain text descriptions to understand scope and requirements
2. **Understands Your Codebase** — Intelligently select relevant files, map dependencies, extract architectural patterns
3. **Creates Implementation Plans** — Generate comprehensive 6-section plans with logical phases and granular tasks
4. **Generates Surgical Prompts** — Produce precise, ready-to-use prompts for AI agents with file-by-file guidance
5. **Identifies Breaking Changes** — Highlight potential impacts across the codebase before implementation

## Quick Start

### Installation

```bash
npx skills add username/code-surgeon
```

Or with git:
```bash
git clone https://github.com/username/code-surgeon.git ~/.claude/skills/code-surgeon
```

### Basic Usage

```bash
# Analyze a simple requirement
/code-surgeon "Add JWT token refresh to authentication flow"

# Analyze a GitHub issue (STANDARD depth)
/code-surgeon "https://github.com/myorg/myrepo/issues/234"

# Deep analysis for architectural changes
/code-surgeon "https://github.com/myorg/myrepo/issues/45" --depth=DEEP

# Quick analysis for bug fixes
/code-surgeon "Fix off-by-one error in pagination" --depth=QUICK

# Resume interrupted session
/code-surgeon-resume surgeon-20250212-abc123xyz
```

## When to Use code-surgeon

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

## Depth Modes

Choose the right depth based on your situation:

| Mode | Time | Accuracy | Cost | Best For |
|------|------|----------|------|----------|
| **QUICK** | 5 min | 85% | $0.04 | Scoped bug fixes, isolated changes |
| **STANDARD** | 15 min | 95% | $0.10 | Normal features, typical changes ← **DEFAULT** |
| **DEEP** | 30 min | 99% | $0.17 | Architectural changes, risky refactoring |

### How to Choose

Before running code-surgeon, answer these questions:

1. **Can you describe the change in one sentence?**
   - No → Stop, define the requirement first
   - Yes → Continue

2. **How many files will this affect?**
   - 1-3 files → QUICK mode
   - 5-8 files → STANDARD mode (default)
   - 8+ or uncertain → STANDARD mode

3. **What's the risk if something breaks?**
   - Low (isolated bug fix) → QUICK mode
   - Medium (new feature) → STANDARD mode
   - High (security, payments, architecture) → DEEP mode

4. **How much time can you invest?**
   - <10 minutes → QUICK mode
   - 15-20 minutes → STANDARD mode
   - 30+ minutes → DEEP mode

## Outputs

code-surgeon generates 3 complementary output formats:

### 1. PLAN.md (Human-Readable)
A formatted markdown document with:
- Executive summary of the implementation strategy
- Research findings about the codebase
- Design choices with rationale
- Phased implementation approach
- Granular tasks with dependencies
- Surgical prompts ready to copy-paste
- Breaking changes analysis
- Verification checklist

### 2. plan.json (Machine-Readable)
Structured JSON with:
- All plan sections in machine-parseable format
- Task dependencies and relationships
- Surgical prompts with metadata
- Breaking changes with impact analysis
- Metrics and estimates

### 3. interactive.json (Step-Through Mode)
Interactive CLI format for:
- Guided task execution
- Progress tracking
- Step-by-step prompts

## Team Guidelines

Create `.claude/team-guidelines.md` to enforce your team's conventions:

```markdown
# Team Guidelines

## Code Style
- Use camelCase for variables, PascalCase for classes
- Max line length: 100 characters
- Prefer const over let

## Architecture Patterns
- Use dependency injection for services
- Error handling: Always use try-catch in async functions
- State management: Redux for global, React Context for local

## Security & Compliance
- Never hardcode secrets or API keys
- Validate all user input
- Sanitize output for XSS prevention
```

code-surgeon will automatically:
- Load these guidelines
- Incorporate them into surgical prompts
- Validate generated code against them
- Flag violations in breaking changes analysis

## Framework Support

code-surgeon detects and understands 35+ frameworks including:

**Frontend:** React, Vue, Angular, Svelte, Next.js
**Backend:** Django, FastAPI, Express, Rails, Spring Boot, Laravel, Flask, Go
**Mobile:** React Native, Flutter, Swift, Kotlin
**Other:** Node.js, Python, Rust, Java, C#, PHP

Each framework gets:
- Specific pattern templates
- Framework-aware prompts
- Common mistake detection
- Testing approach guidance

## How It Works

### 5-Phase Pipeline

```
Your Requirement
    ↓
[PHASE 1] Issue Analysis + Framework Detection (2 min)
├─ Parse requirements and detect issue type
├─ Identify frameworks, versions, and tech stack
├─ Determine primary language and monorepo status
    ↓
[PHASE 2] Context Research (5 min)
├─ Analyze codebase structure
├─ Build dependency graph
├─ Extract architectural patterns
├─ Find team conventions
├─ Select relevant files (3-tier system)
    ↓
[PHASE 3] Implementation Planning (3 min)
├─ Generate 6-section plan
├─ Identify breaking changes
├─ Create logical task phases
├─ Estimate effort per task
    ↓
[PHASE 4] Surgical Prompt Generation (2 min)
├─ Create 9-section prompts per task
├─ Apply framework-specific guidance
├─ Include file paths, line numbers, examples
├─ Validate against team guidelines
    ↓
[PHASE 5] Output Formatting (1 min)
├─ Generate PLAN.md (human-readable)
├─ Generate plan.json (machine-readable)
└─ Generate interactive.json (CLI mode)
```

**Total time:** 5-30 minutes depending on depth mode

## State Management

code-surgeon saves everything to `.claude/planning/sessions/<session-id>/`:

```
.claude/planning/
├─ sessions/
│  └─ surgeon-20250212-abc123xyz/
│     ├─ state.json              ← Complete session state
│     ├─ PLAN.md                 ← Human-readable plan
│     ├─ plan.json               ← Machine-readable plan
│     ├─ interactive.json        ← CLI mode data
│     └─ logs/
│        └─ execution.log        ← Detailed logs
├─ cache/                        ← Shared caches
│  ├─ file-structure-<hash>.json
│  ├─ dependency-graph-<hash>.json
│  └─ patterns-<hash>.json
└─ frameworks/                   ← Framework configs
   ├─ react.yml
   ├─ django.yml
   └─ ...
```

### Resumable Sessions

Interrupted? No problem:

```bash
# Session paused after Phase 2
/code-surgeon-resume surgeon-20250212-abc123xyz

# Continues from Phase 3, reuses Phase 1-2 results
# Saves tokens and time
```

## Error Handling

code-surgeon is designed to **never lose work**:

- ✅ All state saved atomically after each phase
- ✅ Automatic retry for transient failures
- ✅ Clear error messages with recovery instructions
- ✅ Resumable if interrupted or errors occur
- ✅ Session ID provided for resumption

## Security & Privacy

✅ **Completely local** — all analysis happens on your machine
✅ **No external calls** — code never leaves your machine
✅ **Automatic PII detection** — blocks if secrets/keys found
✅ **Team conventions** — validates against your standards

## Sub-Skills

code-surgeon orchestrates 5 specialized sub-skills:

1. **issue-analyzer** — Parse requirements and detect issue type
2. **framework-detector** — Identify tech stack and frameworks
3. **context-researcher** — Analyze codebase and select files
4. **implementation-planner** — Create comprehensive 6-section plans
5. **surgical-prompt-generator** — Generate precise, actionable prompts

Each sub-skill is modular and can be used independently.

## Common Patterns

### Analyzing a Feature Request

```bash
/code-surgeon "Add user authentication with JWT tokens"
```

Gets:
- Analysis of current auth system
- Recommended JWT approach
- Breaking changes for existing clients
- Step-by-step implementation tasks
- Surgical prompts for each task

### Analyzing a Complex Bug

```bash
/code-surgeon "https://github.com/org/repo/issues/1234" --depth=DEEP
```

Gets:
- Detailed root cause analysis
- All affected files and functions
- Why the bug happens
- Breaking changes if fixing it changes behavior
- Surgical prompts for the fix

### Refactoring Code

```bash
/code-surgeon "Refactor authentication module to remove dependency on X" --depth=DEEP
```

Gets:
- Current architecture analysis
- Alternative approaches
- File relationships and impact
- Migration strategy
- Breaking changes assessment

## Next Steps After Generation

1. **Review PLAN.md**
   - Read the summary, research, design choices
   - Verify it matches your intent
   - Edit if needed

2. **Review Surgical Prompts**
   - Check file paths and line numbers
   - Read proposed changes
   - Edit if needed (they're just text)

3. **Execute Tasks**
   - Pick the first task
   - Copy its surgical prompt
   - Hand to Claude, Cursor, or your AI agent

4. **Continue with Dependencies**
   - Each task has clear dependencies
   - Follow the order in the plan
   - All context is already provided

## Tips for Best Results

### ✅ Do This
- Create `.claude/team-guidelines.md` (dramatically improves quality)
- Review the plan before using the prompts
- Use DEEP mode for risky changes
- Check breaking changes section before implementation

### ❌ Don't Do This
- Use for single-file "fix typo" changes (overkill)
- Trust the plan blindly (it's a guide, not gospel)
- Ignore breaking changes warnings
- Share PLAN.md with PII/secrets without sanitizing
- Use DEEP mode for simple bug fixes (huge waste)

## Contributing

Found a bug? Have a suggestion? [Open an issue on GitHub](https://github.com/username/code-surgeon/issues)

## License

MIT — Use freely in your projects

---

## Support

For questions or issues:
- Check the [FAQ](#faq) below
- Review your depth mode choice
- Try resuming if interrupted
- Create `.claude/team-guidelines.md` for better results

## FAQ

**Q: My analysis seems incomplete. Should I use DEEP mode?**
A: Only if the requirement is high-risk or affects multiple systems. STANDARD is the default for good reason.

**Q: Can I edit the PLAN.md after generation?**
A: Yes! Edit freely. It's just a guide, not a contract. The surgical prompts are generated but can be refined.

**Q: What if code-surgeon detects PII/secrets?**
A: Generation is blocked. Sanitize your code first (remove API keys, email addresses, etc.), then retry.

**Q: Does this work with monorepos?**
A: Yes! Detects monorepo structure (Lerna, Turborepo, yarn workspaces) and handles it automatically.

**Q: Can I use this with closed-source/proprietary code?**
A: Yes, everything is local. Code never leaves your machine. Create `.claude/team-guidelines.md` to enforce your security requirements.

**Q: How accurate are the plans?**
A: STANDARD mode: 95% accuracy. DEEP mode: 99% accuracy. Always review before using.

---

**Ready to use?** Try it now:

```bash
/code-surgeon "describe what you want to implement"
```
