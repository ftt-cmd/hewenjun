---
name: github-push
description: Commit and push the current project to GitHub. Use when the user says 提交项目, 推送到GitHub, commit and push, or 上传到仓库. Do not use for Gitee or other Git hosts.
---

# GitHub Push

将当前项目 commit 并 push 到 GitHub 仓库 `https://github.com/ftt-cmd/hewenjun`。工作目录固定为 **当前打开的项目根目录**，所有文件操作都不会超出这个范围。

## 前置检查（必须首先执行）

1. 确认当前 shell 为 **PowerShell 5**，不要使用 bash 语法（无 heredoc `cat <<EOF`、无 `&&`、无 `grep`）。
2. 设置 pager 防止阻塞：`$env:GIT_PAGER = "cat"`。
3. 定位 **当前项目根目录**（即用户打开的工作区文件夹），后续所有操作都在该目录内，绝不 cd 到其他位置。
4. 确保 `git` 可用：优先用 PATH 中的 `git`；若报 "not recognized"，用以下候选路径逐个测试：
   ```powershell
   $gitCandidates = @(
     "$env:LOCALAPPDATA\Programs\Git\cmd\git.exe",
     "$env:ProgramFiles\Git\cmd\git.exe",
     "$env:ProgramFiles(x86)\Git\cmd\git.exe"
   )
   $git = $null
   foreach ($c in $gitCandidates) { if (Test-Path $c) { $git = $c; break } }
   if (-not $git) { Write-Host "ERROR: git.exe not found, please install Git"; return }
   ```
   找到后用 `& $git` 调用，后续步骤中把 `git` 替换为 `& $git`。

## 执行步骤（严格按顺序）

### Step 1 — 仓库级身份配置

仅在本仓库设置，不要污染 global：

```powershell
& $git config user.name "何汶军"
& $git config user.email "hewenjun@users.noreply.github.com"
```

### Step 2 — 初始化 & 远程仓库

```powershell
if (-not (Test-Path ".git")) { & $git init }
```

**主方式：HTTPS 远程地址**（避免 SSH 端口 22 被代理拦截）：

```powershell
$hasOrigin = (& $git remote) -match "origin"
if (-not $hasOrigin) {
    & $git remote add origin https://github.com/ftt-cmd/hewenjun.git
} else {
    & $git remote set-url origin https://github.com/ftt-cmd/hewenjun.git
}
```

### Step 3 — 添加 & 提交

```powershell
& $git add -A
$ts = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
& $git commit -m "chore: commit at $ts"
```

PowerShell 下提交信息必须用单行 `-m`，不要用 heredoc。若报 "nothing to commit"，跳过到 Step 4。

### Step 4 — 推送到 GitHub（含 fallback）

```powershell
$branch = (& $git branch --show-current).Trim()
if (-not $branch) { $branch = "master"; & $git checkout -b master }

# 主方式：HTTPS push
& $git push -u origin $branch 2>&1
$pushOk = ($LASTEXITCODE -eq 0)

# fallback：如果 HTTPS 失败，尝试 SSH（若用户已配置公钥）
if (-not $pushOk) {
    Write-Host "HTTPS push failed, trying SSH fallback..."
    & $git remote set-url origin git@github.com:ftt-cmd/hewenjun.git
    & $git push -u origin $branch 2>&1
    $pushOk = ($LASTEXITCODE -eq 0)
    # 用完 SSH 后改回 HTTPS（防止下次继续被拦）
    & $git remote set-url origin https://github.com/ftt-cmd/hewenjun.git
}
```

如果两种方式都失败，向用户说明并停止：
- HTTPS 失败 → 可能需要 GitHub token / 凭据，提示用户 `git config credential.helper store` 后重试
- SSH 失败 → 提示用户检查公钥是否已添加到 GitHub，或是否有代理拦截 22 端口

## 输出反馈

完成后必须向用户汇报：
- 当前分支名
- 最新 commit hash（前 7 位）
- 远程仓库地址（HTTPS 或 SSH）
- 是否推送成功（如失败，说明原因和建议）
