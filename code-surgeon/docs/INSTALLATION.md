# Installation Guide

## Quick Install

### Via skills.sh Registry
```bash
npx skills add baagad-ai/code-surgeon
```

### Via Direct Clone
```bash
git clone https://github.com/baagad-ai/code-surgeon.git ~/.claude/skills/code-surgeon
```

---

## System Requirements

- Claude (any version)
- Git (for cloning)
- Terminal/Command line access

## Verification

After installation, verify it works:

```bash
# List installed skills
npx skills list

# or in Claude
/code-surgeon "test requirement"
```

You should see code-surgeon listed and be able to invoke it.

---

## Troubleshooting

### Skill Not Found
- Ensure installation path is correct: `~/.claude/skills/code-surgeon`
- Restart Claude/terminal after installation
- Check that SKILL.md file exists in the directory

### Permission Issues
```bash
chmod -R 755 ~/.claude/skills/code-surgeon
```

### Skill Not Loading
- Verify SKILL.md frontmatter is valid
- Check for syntax errors in SKILL.md
- Look at error messages in Claude console

---

## Getting Started

1. **Create team guidelines (optional):**
   ```bash
   cat > .claude/team-guidelines.md << 'EOF'
   # Team Guidelines
   [Add your team's conventions]
   EOF
   ```

2. **Try your first analysis:**
   ```bash
   /code-surgeon "Add feature: JWT token refresh"
   ```

3. **Review the output:**
   - Check generated PLAN.md
   - Review surgical prompts
   - Copy prompts to your AI agent

See [USAGE.md](USAGE.md) for detailed usage instructions.
