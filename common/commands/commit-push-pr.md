# Commit, Push, and Create PR

Commit current changes, push to remote, and create a pull request.

## Pre-computed Context

Current git status:
```
$(git status --short)
```

Recent commits:
```
$(git log --oneline -5)
```

Current branch:
```
$(git branch --show-current)
```

Staged diff summary:
```
$(git diff --cached --stat)
```

## Instructions

1. Review the staged changes above
2. Write a clear, conventional commit message:
   - Use conventional commits format: `type(scope): description`
   - Types: feat, fix, docs, style, refactor, test, chore
   - Keep first line under 72 characters
3. Commit the changes
4. Push to origin (create upstream if needed)
5. Create PR using `gh pr create` with:
   - Clear title matching commit
   - Description summarizing changes
   - Request review if appropriate

## Arguments

$ARGUMENTS

If arguments provided, use them to guide commit message and PR description.

## Example Usage

```
/commit-push-pr Add collision detection to rename script
/commit-push-pr Fix epoch parsing for FLUX2 models
/commit-push-pr
```
