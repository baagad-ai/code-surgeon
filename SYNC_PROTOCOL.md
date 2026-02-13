# Repository Synchronization Protocol

## Overview

This document describes the automated synchronization setup between the development repository (canonical) and the published repository (mirror).

## Repository Structure

### Canonical (Development) Repository
- **Location**: `/Users/prajwalmishra/.claude/skills/code-surgeon/`
- **Purpose**: Primary source for all code-surgeon skill changes
- **Status**: Master branch (main)
- **Access**: Local development

### Published (Mirror) Repository
- **Location**: `/Users/prajwalmishra/Desktop/Experiments/skills/code-surgeon-github/`
- **Purpose**: Public mirror synced automatically from dev repo
- **Remote**: `https://github.com/baagad-ai/code-surgeon.git`
- **Access**: GitHub public repository

## How Synchronization Works

### Automated Post-Commit Sync

After every commit in the development repository, a post-commit hook automatically:

1. **Syncs all files** from dev to published repo
   - Uses `rsync` with `--delete` flag to match dev state exactly
   - Excludes `.git` directory (preserves git history separately)
   - Excludes `.sync.log` to prevent log file commits

2. **Commits changes** in published repo
   - Includes reference to original dev commit hash
   - Preserves original commit message
   - Creates commit with pattern: `sync: Mirror from development repo (commit: HASH)`

3. **Logs all activity** to `.sync.log`
   - Records timestamp of each sync
   - Logs file transfer details
   - Tracks successful syncs and any errors

### Hook Location
```
/Users/prajwalmishra/.claude/skills/code-surgeon/.git/hooks/post-commit
```

### Hook Execution
- **Trigger**: Automatically after every `git commit` in dev repo
- **Scope**: Entire repository except `.git`
- **Idempotent**: Handles cases where no files changed
- **Error Handling**: Exits with status on error, logs details

## File Workflow

1. **Development** → Make changes in dev repo
2. **Commit** → `git commit` in dev repo
3. **Hook Trigger** → Post-commit hook automatically runs
4. **Sync** → Files copied via rsync to published repo
5. **Published Commit** → Hook creates sync commit in published repo
6. **Complete** → Both repos now in sync

## Verification

### Check Sync Status

To verify sync is working:

```bash
# In development repo
git log --oneline -1  # Should show your latest commit

# In published repo
cd /Users/prajwalmishra/Desktop/Experiments/skills/code-surgeon-github
git log --oneline -1  # Should show sync commit with reference to dev commit

# Compare files
diff -r --exclude='.git' . /Users/prajwalmishra/.claude/skills/code-surgeon/
# Should show no differences (except .sync.log in dev)
```

### View Sync Log

```bash
cat /Users/prajwalmishra/.claude/skills/code-surgeon/.sync.log
```

## Manual Recovery Procedure

If automated sync fails for any reason:

### 1. Identify the Problem

```bash
# Check last sync attempt
tail /Users/prajwalmishra/.claude/skills/code-surgeon/.sync.log

# Verify hook is executable
ls -la /Users/prajwalmishra/.claude/skills/code-surgeon/.git/hooks/post-commit
```

### 2. Manual Sync Steps

```bash
# Copy all files from dev to published
rsync -av --delete --exclude='.git' --exclude='.sync.log' \
  /Users/prajwalmishra/.claude/skills/code-surgeon/ \
  /Users/prajwalmishra/Desktop/Experiments/skills/code-surgeon-github/

# Commit changes in published repo
cd /Users/prajwalmishra/Desktop/Experiments/skills/code-surgeon-github
git add -A
git commit -m "sync: Manual recovery sync from development repo

All files synced to match canonical development repository state.
This was a manual sync outside the automatic hook."

# Verify sync
git log --oneline -1
```

### 3. Reinstall Hook (if needed)

```bash
# Make hook executable
chmod +x /Users/prajwalmishra/.claude/skills/code-surgeon/.git/hooks/post-commit

# Verify it works
cd /Users/prajwalmishra/.claude/skills/code-surgeon
git commit --allow-empty -m "test: hook verification"
```

### 4. Clear Log and Resume

```bash
# Clear old log
echo "" > /Users/prajwalmishra/.claude/skills/code-surgeon/.sync.log
```

## Important Notes

- **Never push published repo directly**: Changes will be overwritten by next dev commit sync
- **Never make changes in published repo**: They will be lost on next sync
- **All canonical work**: Must be done in development repo
- **Files always match**: Published repo exactly mirrors dev repo after each commit
- **History is separate**: Dev and published repos maintain independent commit histories
  - Dev repo has full commit history
  - Published repo has full history + sync commits

## Troubleshooting

### Hook Not Running
- Verify hook is executable: `ls -la .git/hooks/post-commit`
- Should show: `-rwxr-xr-x`
- If not executable: `chmod +x .git/hooks/post-commit`

### Files Not Syncing
- Check `.sync.log` for error messages
- Verify published repo path exists: `ls /Users/prajwalmishra/Desktop/Experiments/skills/code-surgeon-github/.git`
- Verify `rsync` is available: `which rsync`

### Sync Takes Too Long
- Normal for initial sync (first time, all files copy)
- Subsequent syncs only copy changed files (much faster)
- Check available disk space if sync stalls

### Published Repo Out of Sync
- Follow manual recovery procedure above
- Verify files match with `diff -r` command
- Check git log to ensure sync commits exist

## Security Considerations

- **No credentials in sync**: Hook uses local filesystem paths only
- **No external network calls**: All sync is local rsync
- **Git credentials**: Not needed for published repo sync
- **Path validation**: Hook checks if published repo exists before syncing

## Maintenance

- Monitor `.sync.log` for errors
- Periodically verify sync status
- Check hook executability after system updates
- Keep this document updated if paths change

## Contact / Support

For issues with sync setup, refer to:
- Hook script: `/Users/prajwalmishra/.claude/skills/code-surgeon/.git/hooks/post-commit`
- Sync logs: `/Users/prajwalmishra/.claude/skills/code-surgeon/.sync.log`
- This document: `/Users/prajwalmishra/.claude/skills/code-surgeon/SYNC_PROTOCOL.md`
