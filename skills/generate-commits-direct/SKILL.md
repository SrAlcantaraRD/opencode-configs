---
name: generate-commits-direct
description: >
  Git commit organizer that applies commits directly instead of generating scripts.
  Trigger: When user asks to "organize and commit changes", "run the direct commit organizer",
  "commit my changes", or invokes /generate-commits-direct.
license: Apache-2.0
metadata:
  author: don_alcantara
  version: "1.1"
---

# Generate Commits Direct

## When to Use

- User wants to organize and commit changes directly
- User asks "organize and commit my changes" or similar
- Direct execution is preferred over generating a shell script

## Critical Patterns

### Pre-Commit Hooks (MUST run before first commit)

```bash
rtk npx @biomejs/biome format --write --diagnostic-level=error
rtk npx @biomejs/biome check --write --diagnostic-level=error
git update-index --again
```

### Commit Message Format

```
<emoji> <type>(<scope>): <short description>
```

- Max 90 characters
- Use appropriate scopes: `auth/domain`, `auth/db`, `infrastructure`, `ui/atoms`, `ui/molecules`, `hooks`, `tests`, etc.

### Emoji → Type Mapping

| Emoji | Type | When |
|-------|------|------|
| ✨ | `feature` | New feature |
| 🐛 | `fix` | Bug fix |
| 📝 | `docs` | Documentation |
| 💄 | `style` | Formatting/style |
| ♻️ | `refactor` | Code refactoring |
| ⚡️ | `perf` | Performance |
| ✅ | `test` | Tests |
| 🔧 | `chore` | Tooling/config |
| 🚀 | `ci` | CI/CD |
| 🗑️ | `revert` | Reverting changes |
| 🔥 | `fix` | Remove code/files |
| 🚚 | `refactor` | Move/rename resources |
| 🏗️ | `refactor` | Architectural changes |
| 📦️ | `chore` | Compiled files/packages |
| ➕ | `chore` | Add dependency |
| ➖ | `chore` | Remove dependency |
| 👔 | `feature` | Business logic |
| 🦺 | `feature` | Validation |
| 🏷️ | `feature` | Types |
| 🩹 | `fix` | Non-critical fix |
| 🚑️ | `fix` | Critical hotfix |
| 🎨 | `style` | Code structure/format |
| 🔒️ | `fix` | Security fix |
| 💚 | `fix` | Fix CI build |
| 🧑‍💻 | `chore` | Developer experience |

### Execution Flow

1. Run pre-commit hooks
2. Inspect `rtk git status` and `rtk git diff` for changed/staged files
3. **Group files by concern** — NEVER bulk commits. Each logical change = separate commit
4. Show proposed commit messages to user for confirmation
5. Execute `rtk git add <files>` + `rtk git commit` for EACH group — no intermediary script
6. Output summary of created commits

### Grouping Strategy

**DO granulate by concern:**
- Theme system refactor → separate commit per sub-concern (remove old files, update imports, simplify layouts)
- Domain changes → separate from UI changes
- Tests → separate commit
- Config/deps → separate commit
- Locales → may bundle with related feature

## Examples

Good commit messages:

- ✨ feature(auth-domain): add user authentication system
- 🐛 fix(ui-match): resolve memory leak in rendering process
- 📝 docs(api): update API documentation with new endpoints
- ♻️ refactor(parser): simplify error handling logic in parser
- 🚨 fix(components): resolve linter warnings in component files
- 🧑‍💻 chore(tooling): improve developer tooling setup process
- 👔 feature(transactions): implement business logic for transaction validation
- 🩹 fix(header): address minor styling inconsistency in header
- 🚑️ fix(auth): patch critical security vulnerability in auth flow
- 🎨 style(components): reorganize component structure for better readability
- 🔥 fix(legacy): remove deprecated legacy code
- 🦺 feature(auth): add input validation for user registration form
- 💚 fix(ci): resolve failing CI pipeline tests
- 📈 feature(analytics): implement analytics tracking for user engagement
- 🔒️ fix(auth): strengthen authentication password requirements
- ♿️ feature(forms): improve form accessibility for screen readers

Example of splitting commits:

- First commit: ✨ feature(auth-domain): add new auth version type definitions
- Second commit: 📝 docs(auth): update documentation for new auth versions
- Third commit: 🔧 chore(deps): update package.json dependencies
- Fourth commit: 🏷️ feature(api): add type definitions for new API endpoints
- Fifth commit: 🧵 feature(worker): improve concurrency handling in worker threads
- Sixth commit: 🚨 fix(lint): resolve linting issues in new code
- Seventh commit: ✅ test(auth): add unit tests for new auth version features
- Eighth commit: 🔒️ fix(deps): update dependencies with security vulnerabilities

```
git add src/ui/themes/*.css src/ui/themes/app-theme-context.tsx
git commit --no-verify -m "♻️ refactor(ui/themes): remove legacy theme files and context"
git add src/ui/component/layouts/bottom-sheet-modal/theme-bottom-sheet.layout.tsx
git commit --no-verify -m "♻️ refactor(ui/layouts): simplify theme-bottom-sheet JSX structure"
...
```

## Command Options

- `--no-verify`: Skip pre-commit hooks (Biome format/check). Use only when explicitly requested.

## Safety Rules

- Do NOT modify file contents except to stage/commit
- Preserve uncommitted changes not included in any commit
- Stage file deletions in appropriate commit

## Commands

```bash
git status
git diff --name-only
git diff --name-only --staged
git add <file>
git commit --no-verify -m "<message>"
git log --oneline -n
```
