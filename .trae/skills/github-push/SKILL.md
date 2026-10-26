---
name: github-push
description: Commit and push the current project to GitHub. Use when the user says 提交项目, 推送到GitHub, commit and push, or 上传到仓库. Do not use for Gitee or other Git hosts.
---

# GitHub Push

将当前项目 commit 并 push 到 GitHub 仓库 `git@github.com:ftt-cmd/hewenjun.git`（SSH，公钥已配置）。

## 前置检查（必须首先执行）

1. 确认当前 shell 为 **PowerShell 5**，不要使用 bash 语法（无 heredoc `cat <<EOF`、无 `&&`、无 `grep`）。
2. 确认 `git` 命令可用：执行 `git --version`。若不可用，停止并提示用户安装 Git。
3. 设置 pager 防止阻塞：`$env:GIT_PAGER = "cat"`。

## 执行步骤（严格按顺序）

### Step 1 — 仓库级身份配置

仅在本仓库设置 user.name / user.email，不要污染 global 配置。若已存在可跳过：

```powershell
git config user.name "何汶军"
git config user.email "hewenjun@users.noreply.github.com"
```

### Step 2 — 初始化 & 远程仓库

若 `.git` 目录不存在：

```powershell
git init
```

检查/设置远程地址（SSH）：

```powershell
$remote = git remote
if ($remote -notmatch "origin") {
    git remote add origin git@github.com:ftt-cmd/hewenjun.git
} else {
    git remote set-url origin git@github.com:ftt-cmd/hewenjun.git
}
```

### Step 3 — 添加 & 提交

```powershell
git add -A
$ts = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
git commit -m "chore: commit at $ts"
```

**注意**：PowerShell 下提交信息用单行 `-m`，不要用 heredoc。若 `git commit` 报 "nothing to commit"，直接跳到 Step 4。

### Step 4 — 推送到 GitHub

首次推送需指定分支名：

```powershell
$branch = git branch --show-current
if (-not $branch) { $branch = "master"; git checkout -b master }
git push -u origin $branch
```

非首次直接：

```powershell
git push
```

若遇到 `Permission denied (publickey)`，提示用户检查 SSH key 是否已添加到 GitHub。

## 输出反馈

完成后必须向用户汇报：
- 当前分支名
- 最新 commit hash（前 7 位）
- 远程仓库地址
- 是否推送成功
