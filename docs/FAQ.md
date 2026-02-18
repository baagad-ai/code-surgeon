# FAQ - Frequently Asked Questions

## General Questions

### What is code-surgeon?
code-surgeon is an AI skill that transforms GitHub issues and requirements into precise, step-by-step implementation plans with surgical prompts—ready to use with Claude or any other AI agent.

### How is it different from just asking Claude to implement a feature?
- **code-surgeon** breaks down the work into phases and tasks with clear dependencies
- Analyzes your entire codebase automatically
- Respects your team's conventions
- Identifies breaking changes proactively
- Generates precise, file-by-file prompts
- Provides 3 output formats for different use cases

### How much does it cost?
- **QUICK mode:** ~$0.04
- **STANDARD mode:** ~$0.10
- **DEEP mode:** ~$0.17

Much cheaper than 30-40 minutes of manual planning.

### How accurate are the plans?
- **QUICK mode:** 85% accuracy (scoped changes)
- **STANDARD mode:** 95% accuracy (normal features)
- **DEEP mode:** 99% accuracy (complex changes)

Always review the plan before using prompts.

---

## Installation & Setup

### Where does code-surgeon install?
```
~/.claude/skills/code-surgeon/
```

### Do I need to set anything up?
No, it's plug-and-play. But creating `.claude/team-guidelines.md` helps quality.

### Can I use it without installing?
No, it needs to be in your Claude skills directory to work.

### Does it need internet?
**Partially.** When you pass a GitHub URL (`/code-surgeon "https://github.com/org/repo/issues/234"`), the skill fetches the issue content from the GitHub API — this is an **external network call**. GitHub issue content is treated as untrusted external data.

**Your codebase is processed through your existing Claude session** — the same channel used for all Claude Code interactions. No additional third-party services receive your code. Only the GitHub issue metadata (title, description, labels) is fetched from GitHub's API, and only when you explicitly provide a GitHub URL. Plain-text requirements (`/code-surgeon "Add JWT refresh"`) trigger no external calls beyond your Claude session.

---

## Usage Questions

### How long does it take?
- **QUICK:** 5 minutes
- **STANDARD:** 15 minutes (default)
- **DEEP:** 30 minutes

### Can I interrupt it?
Yes! Session is saved. Resume with `/code-surgeon-resume <session-id>`

### Can I run it on GitHub issues?
Yes! Pass the GitHub URL: `/code-surgeon "https://github.com/org/repo/issues/234"`

### What if my requirement is vague?
code-surgeon will do its best, but clearer requirements = better plans.

### Can I use it for single-file changes?
Technically yes, but it's overkill. Use for multi-file changes.

### How do I know which depth mode to use?
See [USAGE.md](USAGE.md) for the 4-question decision framework.

---

## Output Questions

### What does PLAN.md contain?
- Summary and research findings
- Design choices with rationale
- Implementation phases and tasks
- **Surgical prompts** (ready to copy-paste)
- Breaking changes analysis
- Verification checklist

### What's the difference between the 3 outputs?
- **PLAN.md** - Read by humans, review and edit
- **plan.json** - Structured data, for tools/CI/CD
- **interactive.json** - Step-through CLI mode

### Can I edit the surgical prompts?
Yes! They're just text. Edit freely for your needs.

### Where are the outputs saved?
```
.claude/planning/sessions/surgeon-<id>/
├── PLAN.md
├── plan.json
├── interactive.json
└── logs/
```

---

## Codebase Questions

### What codebases work?
Any git-based repository with clear structure. Monorepos supported.

### How large can my codebase be?
No practical limit. code-surgeon uses smart file selection.

### Does it work with monorepos?
Yes! Detects Lerna, Turborepo, yarn workspaces.

### What about private code?
Your codebase is processed exclusively through your existing Claude session — the same channel used for all Claude Code interactions. No additional third-party services receive your code. The only separate external call is when you use GitHub issue URLs: the issue's public metadata is fetched from the GitHub API. Private repository issues require authentication and are not accessible without your GitHub token.

### Can I use it with closed-source?
Yes. Your codebase is processed exclusively through your Claude session (same as standard Claude Code). No additional third-party API calls are made beyond your existing Claude session. Create `.claude/team-guidelines.md` to enforce your security rules.

---

## Framework Questions

### How many frameworks are supported?
35+ frameworks including React, Django, Express, Rails, Vue, Angular, Flask, FastAPI, and many more.

### What if my framework isn't detected?
Mention it explicitly: `/code-surgeon "Feature X using [framework]"`

### Can it handle multiple frameworks in one project?
Yes! Works with monorepos and multi-service architectures.

### Does it know the specific versions?
Yes! Detects versions from package managers and applies version-specific guidance.

---

## Team & Collaboration

### How do teams use code-surgeon?
1. PM creates GitHub issue
2. Developer runs code-surgeon
3. Team reviews PLAN.md
4. Developer executes tasks with surgical prompts
5. Reference PLAN.md in PR: "Follows implementation plan from code-surgeon #234"

### Can we enforce team conventions?
Yes! Create `.claude/team-guidelines.md` with your team's rules. code-surgeon will:
- Load it automatically
- Incorporate into surgical prompts
- Validate against it
- Flag violations

### Should we commit the output files?
- **PLAN.md** - Optional, reference in PR
- **state.json** - No, it's temporary
- **plan.json** - Optional, for CI/CD integration

---

## Breaking Changes

### What's included in breaking change analysis?
- **API breaking changes** - Signature changes, removed endpoints
- **Data breaking changes** - Schema changes, migrations
- **Behavior breaking changes** - Behavior alterations affecting users
- **Dependency breaking changes** - Version conflicts, removal

### How does it detect breaking changes?
Analyzes:
- Type signatures
- Function parameters
- Data structures
- API responses
- Dependency versions

### Should I always follow the breaking change warnings?
Yes! These are expert-generated warnings based on impact analysis.

---

## Performance Questions

### How many tokens does it use?
- **SKILL.md loading:** ~12K tokens (once per session)
- **Per phase:** ~8K tokens
- **Total typical session:** ~22K tokens

You have ~100K tokens available, so plenty of room.

### Does it affect my other work?
No. Only the skill file gets loaded initially, then sub-skills as needed.

### Is it fast?
Yes. Total time: 5-30 minutes depending on depth mode.

### Does it cache?
Yes! Saves 25-30% of tokens on repeated analyses in same repo.

---

## Troubleshooting

### Skill not found
- Check installation: `~/.claude/skills/code-surgeon/`
- Restart Claude after installing
- Verify SKILL.md exists

### Permission denied
```bash
chmod -R 755 ~/.claude/skills/code-surgeon
```

### Session won't resume
- Ensure session ID is correct
- Check `.claude/planning/sessions/` exists
- Verify state.json is not corrupted

### PII detection blocked my generation
The Risk Analyzer (Phase 6) flags detected secrets and PII as HIGH severity risks — it identifies them in your codebase but does **not** output their raw content. This includes:
- Hardcoded API keys, tokens, and passwords
- Email addresses and personal data in source files
- Private keys or credentials in config files

To resolve: Remove or move the flagged secrets to environment variables, then re-run.

### Plans seem incomplete
- Try DEEP mode for more thorough analysis
- Check codebase is accessible
- Verify team guidelines aren't conflicting

### Monorepo not detected
- Verify package manager file exists (package.json, go.mod, etc.)
- Check monorepo indicator file (lerna.json, turbo.json, etc.)

---

## Advanced Questions

### Can I use code-surgeon programmatically?
Not yet, but the JSON output is designed for tool integration.

### Can I customize the analysis?
Yes! Create `.claude/team-guidelines.md` to guide analysis.

### Can I add custom frameworks?
Mention in team guidelines:
```markdown
## Framework: [YourFramework]
- [Patterns to follow]
- [Code examples]
```

### Can it handle legacy code?
Yes! It analyzes what exists and works within current patterns.

### Can it suggest refactoring?
Yes! Use: `/code-surgeon "Refactor [module] to [goal]"`

---

## License & Legal

### What's the license?
MIT - Free to use, modify, distribute.

### Can I use this commercially?
Yes! MIT license allows commercial use.

### Can I modify the code?
Yes! MIT license allows modifications.

### Where's the source code?
[github.com/baagad-ai/code-surgeon](https://github.com/baagad-ai/code-surgeon)

---

## Still Have Questions?

- 📖 Read [USAGE.md](USAGE.md) for command reference
- 🏗️ Read [ARCHITECTURE.md](ARCHITECTURE.md) for technical details
- 📚 Read [EXAMPLES.md](EXAMPLES.md) for real-world scenarios
- 🛠️ Read [FRAMEWORKS.md](FRAMEWORKS.md) for framework details
- 💻 Check [SKILL.md](../SKILL.md) for complete reference

Or create a GitHub issue if you think you found a bug!

---

**Last Updated:** February 12, 2026
**Version:** 1.0.0
