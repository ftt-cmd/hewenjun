---
name: github-push
description: Commit and push the current project to GitHub. Use when the user says 提交项目, 推送到GitHub, commit and push, or 上传到仓库. Do not use for Gitee or other Git hosts.
---

# GitHub Push

将当前项目 commit 并 push 到 GitHub 仓库 `https://github.com/ftt-cmd/hewenjun`。

## 课程隔离红线（绝对遵守）

- **Webkaifa / 高级网页设计 / 任何其他课程文件夹是独立实体**
- ✅ **允许**：只读调用另一个课程文件夹里**已安装好的程序**（如 git.exe），这是使用工具，不是写文件
- ❌ **禁止**：在其他课程文件夹内搜索、读取、创建、修改、删除任何文件
- ❌ **禁止**：`cd` 到其他课程文件夹
- 所有 **读写操作** 严格限制在当前打开的项目根目录内

## 前置检查

1. 当前 shell 为 **PowerShell 5**。不要用 bash 语法（无 heredoc、无 `&&`、无 `grep`）。
2. `$env:GIT_PAGER = "cat"` 防止阻塞。
3. **定位项目根目录**：所有文件操作都在此目录内。

4. **git 可用性检查** — 按以下顺序探测，找到即停：
   ```powershell
   $git = $null
   # ① PATH 中的 git
   try { & git --version 2>$null; if ($LASTEXITCODE -eq 0) { $git = "git" } } catch {}
   # ② 中性系统安装路径
   if (-not $git) {
       $neutralPaths = @(
           "$env:LOCALAPPDATA\Programs\Git\cmd\git.exe",
           "$env:ProgramFiles\Git\cmd\git.exe",
           "${env:ProgramFiles(x86)}\Git\cmd\git.exe"
       )
       foreach ($p in $neutralPaths) {
           $expanded = $ExecutionContext.InvokeCommand.ExpandString($p)
           if (Test-Path $expanded) { $git = $expanded; break }
       }
   }
   # ③ 用户告知的只读 fallback：Webkaifa 课程里已安装的 git.exe（只读调用，不修改该文件夹内任何文件）
   if (-not $git -and (Test-Path "D:\Webkaifa\ruanjian\git软件安装\Git\cmd\git.exe")) {
       $git = "D:\Webkaifa\ruanjian\git软件安装\Git\cmd\git.exe"
   }
   # 全部找不到就停
   if (-not $git) {
       Write-Host "ERROR: git 未找到。请在本机 PowerShell 手动 push。"
       return
   }
   Write-Host "Using git: $git"
   ```

## 执行步骤

### Step 1 — 仓库级身份配置

仅本仓库设置，不污染 global：

```powershell
& $git config user.name "何汶军"
& $git config user.email "hewenjun@users.noreply.github.com"
```

### Step 2 — 初始化 & 远程仓库

```powershell
if (-not (Test-Path ".git")) { & $git init }
```

**主用 HTTPS**（凭据已通过 `credential.helper store` 存好，直接用）：

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

PowerShell 下提交信息必须用单行 `-m`。若报 "nothing to commit" 跳到 Step 4。

### Step 4 — 推送

```powershell
$branch = (& $git branch --show-current).Trim()
if (-not $branch) { $branch = "master"; & $git checkout -b master }

# 主方式：HTTPS（凭据已存好，无需输入）
& $git push origin $branch
$pushOk = ($LASTEXITCODE -eq 0)

# fallback：HTTPS 失败则换 SSH（用完改回 HTTPS）
if (-not $pushOk) {
    Write-Host "HTTPS push 失败，尝试 SSH fallback..."
    & $git remote set-url origin git@github.com:ftt-cmd/hewenjun.git
    & $git push origin $branch
    $pushOk = ($LASTEXITCODE -eq 0)
    & $git remote set-url origin https://github.com/ftt-cmd/hewenjun.git
}
```

若 HTTPS 和 SSH 都失败，向用户说明原因和建议。

## 输出反馈

完成后必须汇报：
- 当前分支名
- 最新 commit hash（前 7 位）
- 远程仓库地址（HTTPS 或 SSH）
- 推送是否成功（失败则说明原因和建议）
