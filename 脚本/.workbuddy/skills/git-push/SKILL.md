---
name: git-push
description: This skill should be used when the user asks to push, commit and push, sync to GitHub, or says things like "推送一下"、"推上去"、"提交并推送"、"同步到 GitHub" for the statistics-learning repository. It automates the full flow: stage changes, generate a Conventional Commit message, commit, and push to origin/main on GitHub.
agent_created: true
---

# Git Push (statistics-learning)

## Overview

Automate the one-sentence push workflow for the `statistics-learning` repository: stage all meaningful changes, write a clean Conventional Commit message, commit, and push to GitHub. Trigger phrases include "推送一下", "推上去", "提交并推送", "同步到 GitHub", "push 一下".

## Repository Facts

- Repo root: `C:/Users/30130/Desktop/repos/statistics-learning` (the workspace folder `脚本/` is a subdirectory; run git with `git -C "C:/Users/30130/Desktop/repos/statistics-learning" ...` or from the repo root)
- Remote: `origin` → `https://github.com/Evsang-0407/statistics-learning.git`
- Default branch: `main` (tracks `origin/main`)

## Workflow

### Step 1: Inspect

1. Run `git -C <repo-root> status --porcelain` and `git -C <repo-root> diff --stat` to see what changed.
2. If there is nothing to commit, still run `git push` if the local branch is ahead of `origin/main`; otherwise report "everything is already up to date" and stop.

### Step 2: Stage

1. Stage all changes with `git -C <repo-root> add -A`.
2. Exclude generated junk by first un-staging (or not adding) paths matching: `__pycache__/`, `.ipynb_checkpoints/`, `*.pyc`, `.DS_Store`, `Thumbs.db`. If such files were already tracked, leave them alone — do not rewrite history.

### Step 3: Commit

1. Summarize the staged changes from `git status --porcelain` and `git diff --cached --stat`.
2. Write ONE commit using Conventional Commit format. Choose a type that matches the content:
   - `feat:` new scripts, code, or notebooks (e.g. `feat: 新增第2章假设检验脚本`)
   - `fix:` bug fixes
   - `docs:` README or `notes/` changes
   - `data:` dataset additions/updates under `datasets/`
   - `chore:` tooling, config, `.workbuddy/` skill files
3. Commit message rules:
   - Subject line under 50 chars, Chinese or English matching the change's nature, no trailing period.
   - Add a short body paragraph only when the change spans multiple unrelated areas; otherwise subject only.
   - Use a heredoc to preserve formatting:
     ```bash
     git -C "<repo-root>" commit -m "$(cat <<'EOF'
     feat: 新增方差分析练习脚本
     EOF
     )"
     ```
4. Never use `--no-verify`, `--amend` on pushed commits, or sign-bypass flags.

### Step 4: Push

1. Run `git -C <repo-root> push origin main`.
2. If the push is rejected (remote has new commits):
   - Run `git -C <repo-root> pull --rebase origin main`.
   - If rebase conflicts occur, STOP and report the conflicting files to the user; do not auto-resolve.
   - If rebase succeeds cleanly, push again.
3. Never use `--force` or `--force-with-lease` unless the user explicitly asks and confirms.

### Step 5: Report

Reply concisely with: commit hash (short), commit message, number of files changed, and the push result. Example: `✅ 已推送 a1b2c3d "feat: 新增第2章脚本"（5 个文件）到 origin/main`.

## Safety Rules

- Destructive operations (`reset --hard`, `push --force`, `rebase -i`, `clean`) require explicit user confirmation first.
- Do not push to any remote other than `origin`, and do not change branch away from `main` during this workflow.
- If credentials fail, report the git credential error verbatim instead of retrying in a loop.
