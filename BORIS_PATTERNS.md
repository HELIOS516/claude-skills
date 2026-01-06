# Boris Cherny's Claude Code Patterns

> Reference document based on Boris Cherny's January 2, 2026 setup thread.
> Boris is the creator of Claude Code at Anthropic.

## Core Philosophy

"There is no one correct way to use Claude Code: we intentionally build it in a way that you can use it, customize it, and hack it however you like."

**Key insight:** "Anytime we see Claude do something incorrectly we add it to the CLAUDE.md, so Claude knows not to do it next time." Every mistake becomes a rule.

---

## 1. Parallel Execution (5+ Claudes)

### Terminal Setup
- Run 5 Claude instances in parallel
- Number tabs 1-5 for easy tracking
- Use iTerm2 system notifications for input alerts

**iTerm2 Config:**
Preferences → Profiles → Terminal → ✓ "Silence bell" → ✓ "Send notification on idle"

### Web + Local Hybrid
- Run 5-10 additional Claudes on claude.ai/code
- Hand off sessions with `&` command or `--teleport`
- Start sessions from iOS app, check in later

### Conflict Prevention
- Use git worktrees for parallel work on same repo
- Each Claude works on isolated branch/worktree
- Prevents merge conflicts between parallel sessions

---

## 2. CLAUDE.md (Shared Memory)

### Location & Purpose
- Single shared file checked into git root
- Entire team contributes multiple times per week
- Auto-injected into every Claude session

### Content Categories
1. **Mistakes & Corrections** - What Claude got wrong → how to do it right
2. **Conventions** - Code style, naming, patterns
3. **Domain Knowledge** - Project-specific context
4. **Edge Cases** - Known gotchas and workarounds
5. **DO NOT Rules** - Explicit prohibitions

### Update Workflow
```
1. Claude makes mistake
2. Human catches in review
3. Add rule to CLAUDE.md
4. Commit with PR
5. All future sessions inherit learning
```

---

## 3. Plan Mode First

### Mode Switching
- **Shift+Tab once:** Toggle auto-accept edits
- **Shift+Tab twice:** Enter Plan mode

### Workflow
1. Start session in Plan mode
2. Iterate on plan with Claude until solid
3. Switch to auto-accept mode
4. Claude executes (often one-shot)

**Key insight:** A good plan dramatically improves execution quality.

---

## 4. Slash Commands

### Location
`.claude/commands/*.md` (checked into git)

### Structure
```markdown
# Command description
Optional instructions with $ARGUMENTS placeholder
Inline bash for pre-computation: $(git status --short)
```

### Boris's Daily Driver: `/commit-push-pr`
Used dozens of times daily. Pre-computes git status to avoid model back-and-forth.

### Benefits
- Saves repeated prompting
- Claude can invoke these too
- Team shares via git

---

## 5. Subagents

### Location
`.claude/agents/*.md`

### Boris's Subagents
- **code-simplifier** - Simplifies code after Claude finishes
- **verify-app** - Detailed E2E testing instructions

### Use Cases
- Post-execution refinement
- Specialized verification
- Multi-agent orchestration

---

## 6. Hooks

### PostToolUse Hook
Auto-format code after Claude edits (handles last 10%).

**Location:** `.claude/hooks/`

```json
{
  "event": "PostToolUse",
  "tools": ["write_file", "edit_file"],
  "command": "prettier --write $FILE"
}
```

### Stop Hook
Trigger verification when Claude finishes a task.

---

## 7. Permissions

### Pre-Authorization
Use `/permissions` to allow safe commands upfront.
Avoids repeated permission prompts.

**Location:** `.claude/settings.json`

```json
{
  "permissions": {
    "allow": [
      "git status",
      "git diff",
      "git log",
      "npm test",
      "python -m pytest"
    ]
  }
}
```

### For Long-Running Tasks
- `--permission-mode=dontAsk` in sandbox
- `--dangerously-skip-permissions` (use carefully)

---

## 8. MCP Integrations

### Common Servers
- **Slack** - Search/post messages
- **BigQuery** - Analytics queries via bq CLI
- **Sentry** - Error logs
- **Puppeteer** - Browser automation

### Configuration
`.mcp.json` checked into git for team sharing.

---

## 9. GitHub Integration

### @claude Tagging
During code review, tag `@.claude` on PRs to:
- Update CLAUDE.md with learnings
- Auto-fix issues
- Add documentation

### Setup
`/install-github-action` creates `.github/workflows/claude.yml`

---

## 10. Verification Loops

> "Give Claude a way to verify its work. If Claude has that feedback loop, it will 2-3x the quality of the final result."

### Domain-Specific Verification
| Domain | Verification Method |
|--------|---------------------|
| CLI tools | Run command, check output |
| Backend | Run test suite |
| Frontend | Browser automation (Chrome extension) |
| Mobile | Phone simulator |
| Data | Query validation |

### Long-Running Verification
- Background agent verification post-task
- Stop hooks for deterministic checks
- `ralph-wiggum` plugin for autonomous operation

---

## 11. Model Selection

**Boris uses Opus 4.5 with thinking for everything.**

Rationale:
- Best coding model available
- Less steering required
- Better tool use
- Slower generation BUT faster overall (fewer corrections)

> "Even though it's bigger & slower than Sonnet, since you have to steer it less and it's better at tool use, it is almost always faster than using a smaller model in the end."

---

## 12. Long-Running Autonomous Tasks

For tasks that run for extended periods without human intervention.

### Permission Modes
```bash
# In a sandbox environment
claude --permission-mode=dontAsk

# Nuclear option (use carefully)
claude --dangerously-skip-permissions
```

### Ralph Wiggum Plugin

Boris's approach for autonomous operation (named after the Simpsons character who says "I'm helping!").

**What it does:**
- Runs Claude in a loop for extended autonomous work
- Handles task continuation when context fills up
- Auto-restarts on failures
- Maintains task state across sessions

**Installation:**
```bash
# From official plugins repo
claude plugins install ralph-wiggum
```

**Usage:**
```bash
# Start autonomous task
claude --plugin ralph-wiggum "Refactor the entire auth module"
```

**Source:** [github.com/anthropics/claude-plugins-official/tree/main/plugins/ralph-wiggum](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/ralph-wiggum)

### Verification for Long Tasks

Three approaches Boris uses:

1. **Prompt verification** - Ask Claude to verify with background agent when done
2. **Stop hooks** - Deterministic verification triggered on task completion
3. **Ralph Wiggum** - Plugin handles continuation and verification

### Example: Autonomous Refactoring
```bash
# Start long task with auto-verification
claude --permission-mode=dontAsk \
  "Refactor all API handlers to use the new error format. 
   When done, run the test suite and fix any failures.
   Continue until all tests pass."
```

---

## 13. Session Management

### Teleport
`--teleport` switches sessions between local and web.

### iOS Workflow
Start sessions from phone in morning, check in later from desktop.

### Context Compaction
Long sessions auto-compact. Use `/context` to see what's retained.

---

## Quick Reference

| Pattern | Location | Frequency |
|---------|----------|-----------|
| CLAUDE.md | repo root | Update on mistakes |
| Slash commands | .claude/commands/ | Use daily |
| Subagents | .claude/agents/ | Post-PR |
| Hooks | .claude/hooks/ | Always running |
| Permissions | .claude/settings.json | Set once |
| MCP servers | .mcp.json | Set once |
| Ralph Wiggum | claude plugins | Long tasks |
| GitHub Action | .github/workflows/ | Set once |

---

## Resources

- [Terminal Notifications](https://code.claude.com/docs/en/terminal-config#iterm-2-system-notifications)
- [Slash Commands](https://code.claude.com/docs/en/slash-commands)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks Guide](https://code.claude.com/docs/en/hooks-guide)
- [Chrome Extension](https://code.claude.com/docs/en/chrome)
- [Ralph Wiggum Plugin](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/ralph-wiggum)
