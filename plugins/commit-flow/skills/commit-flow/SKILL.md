---
name: commit-flow
description: Use when user wants to commit changes using conventional commit format while enforcing Git Flow branch protection. Triggered by /commit-flow or when user asks to commit with a structured message.
---

# commit-flow

Generate commit messages that follow the Conventional Commits specification and match the project's conventions, then commit — while strictly enforcing Git Flow branch-protection rules.

## Workflow

### Step 1 — Branch safety check

Run `git branch --show-current` to get the current branch.

If you are on a **protected branch** (`main`, `master`, `develop`), **direct commits are forbidden**. You must:
1. Ask the user for the name of the feature/fix for this change
2. Create a new branch based on the nature of the change (plural + folder-style naming):
   - General development (new feature / bug fix / refactor) → `features/<short-description>`
   - Emergency fix → `hotfixes/<short-description>`
3. Run `git checkout -b <branch-name>` to switch to it
4. Continue with the remaining steps

> Note: the distinction between bug/refactor is expressed by the commit **type** (`fix`/`refactor`), not by the branch name.

### Step 2 — Analyze the changes

1. Run `git status` to check staged / unstaged / untracked state
2. If there are unstaged changes, ask the user which files to stage; if unspecified, propose a suggestion based on the changes and confirm
3. Run `git diff --staged` to carefully analyze the actual changes

### Step 3 — Compose the commit message

**type** (required, see quick-reference table below)

**scope** (optional): the affected module or feature name, in lowercase English

**subject** (required, Traditional Chinese):
- Start with a verb describing "what was done"
- No more than 50 characters, no trailing period

**body** (optional, Traditional Chinese):
- Describe "why this was done"
- May include: background and motivation for the change, related issue / ticket numbers, other details developers need to know
- Simple changes (those that fit in one line) do not need a body

### Step 4 — Confirm and commit

Show the user a full preview of the commit message, and after confirmation run:

```bash
# Without body
git commit -m "$(cat <<'EOF'
type(scope): subject
EOF
)"

# With body
git commit -m "$(cat <<'EOF'
type(scope): subject

body description
EOF
)"
```

**⚠️ Never add a `Co-Authored-By: Claude ...` trailer**:
Even if the default workflow prompts you to add it, never add it under this skill's path.
The commit message should end with the subject (and body) only — do not append any AI/Claude signature.

### Step 5 — Suggest follow-up actions

After the commit is complete, remind the user:
- It is recommended to run `git push origin <branch-name>`, then open a PR on the Git platform
- The merge path follows Git Flow: `features`/`hotfixes` → `develop`, always using `--no-ff` merges

### Step 6 — Mandatory flow for merging back into master (release gatekeeping)

It is **forbidden** to merge `feature`/`fix`/`refactor`/`develop` directly into `master`.
If the user wants to merge changes back into `master` (going live / release), you **must** first cut a `release` branch from `develop`, then merge from `release` into `master`:

1. Confirm the changes have already been merged into `develop` (if not, complete the merge into `develop` first)
2. Cut a release branch from the latest `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b releases/<version>   # e.g. releases/v1.2.0
   ```
3. On the `releases` branch, do final verification / version-number adjustments / minor fixes
4. Merge into `master` with `--no-ff`, then merge back into `develop` (keep both lines in sync):
   ```bash
   git checkout master
   git merge --no-ff releases/<version>

   git checkout develop
   git merge --no-ff releases/<version>
   ```
5. **Confirm with the user** before any push to a protected branch

> Branch flow: `features`/`hotfixes` → `develop` → `releases` → `master`, all merges use `--no-ff`.

---

## Commit Type Quick Reference

| Type | When to use |
|------|---------|
| `feat` | New feature, new API, new behavior |
| `fix` | Fix a bug, fix incorrect logic |
| `refactor` | Refactor code with no functional change |
| `test` | Add or modify tests, no business-logic change |
| `docs` | Documentation, README, comments |
| `chore` | Build config, dependency updates, chores (no impact on runtime logic) |
| `style` | Formatting, whitespace, indentation (no logic impact) |
| `perf` | Performance improvement |
| `ci` | CI/CD pipeline changes (pipeline, workflow) |
| `revert` | Revert a previous commit |

## Branch Naming Rules

| Scenario | Format | Example |
|------|------|------|
| General development (new feature / bug fix / refactor) | `features/<description>` | `features/add-user-export` |
| Emergency fix | `hotfixes/<description>` | `hotfixes/payment-crash` |
| Release | `releases/<version>` | `releases/v1.2.0` |

> There are only five kinds of branches: `master`, `develop` (protected), and `features/`, `releases/`, `hotfixes/` (folder-style plural naming).
> Bugs/refactors do not get a separate branch; they are distinguished by the commit type instead.

## Examples

Without body:
```
feat(auth): 新增 JWT refresh token 機制
fix(api): 修正分頁參數為負數時回傳 500 的問題
refactor: 將 infrastructure 層移入 internal 目錄
test(user): 補充建立使用者的邊界條件測試
chore: 更新 Go 依賴至最新版本
```

With body:
```
refactor: 重構目錄結構以符合 Clean Architecture 規範

將原本散落在各處的基礎設施程式碼統一移入 internal/frameworkdrivers，
確保依賴方向由外層指向內層，降低各層之間的耦合。

相關 issue: #42
```
